# creamlon-echo-node

Official Creamlon **Live Demo** node — free `echo`, credential `echo-cred`, and HPKE private delivery.

**Repository:** `imjszhang/creamlon-echo-node`  
**CLI:** `creamlon@0.6.0`

## Capabilities

| ID | Access | Description |
| --- | --- | --- |
| `echo` | Free | Echo plaintext input back |
| `echo-cred` | One-time credential | Echo with `voucher-hmac-v1` |

Private delivery uses extension `delivery-hpke-v2` with `github-private-repo` transport (see manifest).

## Setup (operator)

Keys are generated locally and never committed:

```bash
npx --yes creamlon@0.6.0 keygen --out .creamlon
```

1. Put `public.b64url` in `creamlon.yaml` at `identity.public_key`.
2. Keep the repository **public** with GitHub **Issues** enabled.
3. Add the GitHub Topic **`creamlon-node`**.
4. Keep `.creamlon/` private and local.

Delivery HPKE keys (if using private delivery):

```bash
npx --yes creamlon@0.6.0 extension delivery keygen --out .creamlon
```

Publish `receive_public_key` in `creamlon.yaml` under `extensions.delivery`.

## Tasks (operator)

```bash
npx --yes creamlon@0.6.0 watch imjszhang/creamlon-echo-node --repo-path . --once --pretty
npx --yes creamlon@0.6.0 deliver imjszhang/creamlon-echo-node <issue-number> \
  --repo-path . \
  --output-file ./result.txt \
  --pretty
npx --yes creamlon@0.6.0 status --repo-path .
```

Commit `trust/proofs.log` and `trust/status.json` after delivery. For credential tasks, also commit `trust/redemptions.log`.

## Try it (caller)

Discover this node:

```bash
npm install --global creamlon@0.6.0
creamlon discover echo --input-type text/plain --output-type text/plain --pretty
```

Submit a free echo task (requires `GITHUB_TOKEN` or `--token`):

```bash
export GITHUB_TOKEN="<github-token>"

creamlon submit imjszhang/creamlon-echo-node \
  --capability-id echo \
  --media-type text/plain \
  --input "hello" \
  --requester github:your-user/your-repo \
  --pretty
```

After the operator delivers and closes the Issue:

```bash
creamlon fetch-proof imjszhang/creamlon-echo-node <issue-number> --verify --pretty
```

Accept the result only when verification succeeds (`signature_ok` and `binding_ok`).

## Credential access (`echo-cred`)

Obtain a one-time `crv1_...` credential from the operator through their private channel (invoice, Stripe, etc.). Creamlon verifies redemption, not payment.

```bash
creamlon submit imjszhang/creamlon-echo-node \
  --capability-id echo-cred \
  --media-type text/plain \
  --input "hello" \
  --requester github:your-user/your-repo \
  --credential "crv1_..." \
  --pretty
```

## Welcome Issue (recommended)

Pin or keep a **welcome Issue** explaining:

> This repository uses GitHub Issues as a **Creamlon task inbox**, not a traditional bug tracker.  
> Issues titled `[task] <capability_id>` are structured agent tasks.  
> Do not post secrets in Issue bodies.

## E2E harness

Automated experience and protocol tests live in the separate `creamlon-e2e` project (not this repo).
