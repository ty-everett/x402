# PR #6 follow-up: current-main runtime pins

This directory delivers a patch for [bsv-blockchain/x402#6](https://github.com/bsv-blockchain/x402/pull/6). It does not change active workflows or merge/rebase that PR. Save the patch before switching branches; apply it to a fresh branch at the reviewed upstream baseline.

## Baseline and scope

- Official main: `1a084343e165d6f61b60b34f6146d6e4a4a7f3ca`.
- Original #6: `3eed0e50a4d78b55b9c482c89d771d7ff0900ef7`.
- Same version choices as #6: Node `22` → `22.23.4`, `24` → `24.22.1`, including the existing unit-test matrix. These are #6's selected releases, not a claim about today's latest Node releases.
- 39 workflow files, compared with 34 in #6. Newly covered: payment-channel codegen and the Canton, Cardano, Casper, and XRPL publishers.
- Only Node version selectors change. Triggers, permissions, step ordering, actions, application code, dependencies, and matrix membership are preserved.

Upstream moved E2E Node/pnpm setup before dependency installation and family selection. #6 edits the now-deleted old setup block, causing a modify/delete merge conflict. This patch pins the setup at its current location; it does not restore the old block or duplicate initialization.

## Apply without rewriting the original branch

From a checkout of this follow-up branch:

```sh
cp .github/patches/node-runtime-pins-main-20261115.patch ../node-runtime-pins-main-20261115.patch
git fetch https://github.com/x402-foundation/x402.git main
git switch -c review/node-runtime-pins-current-main 1a084343e165d6f61b60b34f6146d6e4a4a7f3ca
git apply --check ../node-runtime-pins-main-20261115.patch
git apply ../node-runtime-pins-main-20261115.patch
git diff --check
git diff --stat
```

If upstream main has advanced, review the new baseline and repeat the applicability checks before committing. The author decides how to update or retarget #6; no force-push, merge, or branch replacement is proposed here. This patch replaces the original pinning delta on the new baseline; it is not meant to be applied on top of the old already-pinned #6 tree.

## Validation

- Clean `git apply --check`, actual application, reverse-applicability, and whitespace checks on the stated official main.
- Parsed all changed YAML and compared its structure before/after, allowing only the two Node version substitutions. Confirmed all targeted floating selectors were replaced and no non-workflow files changed.
- Prettier results are unchanged from the upstream baseline for all 39 files; 21 already have formatting warnings. This patch deliberately preserves unrelated formatting and does not claim a clean repository-wide format check.
- Also passes application preflight on BSV exact draft #7 head `d89ccb7d8104cf1a5b4839461e73980f4e4532e8`. Its separately added BSV publisher is already pinned to `24.22.1`.
- Separate compatibility runs on that exact head passed core/BSV builds, BSV typecheck/format/lint, 124 BSV tests, 854 core tests, and scoped exports under both Node `22.23.4` and `24.22.1`. These are local macOS checks, not a complete Linux workflow run.
- No workflow, deployment, publishing, funded-wallet integration, or public-node RPC was executed for this patch.

Prepared with AI assistance. Human review and normal maintainer CI authorization remain required.
