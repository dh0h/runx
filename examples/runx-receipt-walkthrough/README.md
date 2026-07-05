# Receipt-First runx Smoke Test

This short walkthrough is a public support note for
[runx](https://github.com/runxhq/runx) and the hosted package surface at
[runx.ai](https://runx.ai). It is meant for a maintainer or new contributor who
wants to see the practical value of runx without reading the whole architecture
first.

The useful idea: a runx skill should leave behind a checkable receipt. That
receipt makes an agent run easier to review because the input shape, output
packet, runner metadata, and signing information are attached to the result
instead of living only in a chat transcript.

## One Local Check

From a checkout of `runxhq/runx`, build the native CLI and run the checked-in
hello-world skill with a demo signing key:

```bash
cargo build --manifest-path crates/Cargo.toml -p runx-cli

export RUNX_RECEIPT_SIGN_KID=runx-demo-key
export RUNX_RECEIPT_SIGN_ED25519_SEED_BASE64=QkJCQkJCQkJCQkJCQkJCQkJCQkJCQkJCQkJCQkJCQkI=
export RUNX_RECEIPT_SIGN_ISSUER_TYPE=hosted
export RUNX_RECEIPT_DIR="$(mktemp -d)"

crates/target/debug/runx skill examples/hello-world \
  --message "hello from a receipt-first smoke test" \
  --non-interactive \
  --json
```

The JSON response should include a sealed status and a receipt id. Keep that id
and inspect it:

```bash
crates/target/debug/runx history <receipt-id> --json
```

This gives a reviewer something concrete to compare against the claimed
behavior. For local smoke tests the demo key is intentionally public; production
runs should use a real signing key and the matching verification key.

## Why This Is Worth Sharing

- It gives a new contributor a small success path before they try a larger
  graph, registry publish, or MCP integration.
- It explains runx in terms of reviewable evidence: "what happened, with what
  inputs, and where is the receipt?"
- It points readers to the actual project and public package surface instead of
  asking for stars, follows, or reciprocal promotion.
- It is specific to runx's governed execution model, so it should be useful to
  someone deciding whether to adopt or contribute to the project.
