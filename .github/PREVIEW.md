# Preview & CI Pipeline

This repository is a **Node.js library / Claude Code plugin collection** (`ecc-universal`) — a
bundle of agents, skills, hooks, commands, rules, and MCP configs distributed via npm. It is
**not** a web, mobile, or desktop app, so there is nothing to deploy to GitHub Pages or to
package as an installer. The appropriate preview tier is **CI validation**.

## Tiers in this repo

| Tier | Workflow | What it does |
|------|----------|--------------|
| Test CI | `.github/workflows/ci.yml` (**CI**) | Installs deps and runs the full validator + test chain (`npm test`) on Node 18 and 20. A separate non-blocking `lint` job runs ESLint + markdownlint. |

### What `npm test` runs

The `test` script chains the CI validators plus the unit suite:

```
check-unicode-safety  -> validate-agents -> validate-commands -> validate-rules
-> validate-skills -> validate-hooks -> validate-install-manifests
-> validate-no-personal-paths -> catalog:check -> tests/run-all.js
```

## Triggers

- `push` to `main` and `claude/repo-organization-artifacts-tnwgtb`
- `pull_request` targeting `main`
- Manual `workflow_dispatch`

## One-time setup

- **None.** No GitHub Pages source toggle, no secrets, and no backend are required.

## Caveats

- **No deployable preview.** This is a config/CLI bundle, not a running app; there is no live
  URL to preview and no build artifact to ship. Validation that the bundle is well-formed and
  that tests pass is the deliverable.
- **Package manager.** `package.json` pins `yarn@4.9.2` via `packageManager`, but both
  `package-lock.json` and `yarn.lock` are committed. CI uses `npm install` for simplicity;
  `yarn install` would also work.
- **Lint is non-blocking.** The `lint` job is `continue-on-error: true` so style/markdown
  findings surface without failing the build. Flip that off once lint is clean if you want it
  enforced.
- **Node engines.** `engines.node` requires `>=18`; CI exercises 18 and 20.
