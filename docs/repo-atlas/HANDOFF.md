# Repo Atlas — Session Handoff

> For the next Claude Code session. Read this + INVENTORY.md + STATUS.md in this
> directory to fully restore context. Last updated 2026-07-01.

## State

- **Phase 1 (triage): done.** All 27 repos inventoried → `INVENTORY.md`. 27 → ~13–15 keepers; 12 flagged for archive (NOT yet archived).
- **Phase 3 (preview pipelines): done.** Tailored CI on branch `claude/repo-organization-artifacts-tnwgtb` in every keeper. Dashboard in `STATUS.md`.
- **Privacy decision:** repos stay private → **no GitHub Pages anywhere** (Pages from private repos is publicly accessible). Web preview tier = downloadable `dist` artifact; `og_iching_v2_emergent`'s web-preview.yml already converted (commit 54e8b70). Live URLs, if wanted, via Netlify/Vercel private-repo integration.
- **Atlas home:** user created `DirtyBananas/Atlas` as the dedicated index repo. Prior sessions could not reach it (repo scope fixed at session start) — the new session must include it in scope.

## Next steps, in order

1. **Move the atlas** — copy `INVENTORY.md`, `STATUS.md`, and this file from `everything-claude-code:docs/repo-atlas/` (branch `claude/repo-organization-artifacts-tnwgtb`) into `DirtyBananas/Atlas` with a README that makes Atlas the canonical dashboard. Optionally remove the copies here afterward.
2. **Verify og web-preview artifact run** went green after the Pages→artifact conversion (commit 54e8b70) and update STATUS.md if needed.
3. **Confirm user toggle:** Actions enabled on `ruflo` + `everything-claude-code` → verify their pushed CI registers and runs.
4. **Phase 2 archiving** (needs explicit user greenlight, reversible): archive the 12 repos listed in INVENTORY.md (start with empty `iching2`, `iching3`, then the iching also-rans, `saph1`, `balconfig`, `snapxapp-emerge`, `privateephemeralbrowser`).
5. **Optional fix pending user decision:** `screencalltoaction` APK — bump `react-native` 0.79.5 → 0.81.5 (+ react/react-dom/native deps) in `frontend/package.json` to match Expo SDK 54's generated `MainApplication.kt` (`loadReactNative`). Its web build is already green.
6. **Merge pipeline branches to `main`** per keeper repo once the user is satisfied (workflows also trigger on main).

## Gotchas learned (don't relearn these)

- These AI-generated repos mostly have **no committed lockfiles** → never use setup-node `cache:` or `npm ci` without verifying the lockfile exists at that exact path.
- **No Pages deploys** — user keeps repos private; Pages from private repos publishes a public site. Use artifact uploads (or Netlify/Vercel integration, user-connected).
- Expo web builds: patch `app.json` at build time only → `web.output="single"` (avoids SSR `window` crash). No baseUrl patch needed for artifact/root serving.
- Committed `gradlew` files lack the exec bit → `chmod +x` before calling.
- electron-builder auto-publishes if a repo is configured → always `--publish never` in CI artifact builds.
- `skyline-estimator` lint (124 errors) and tests are `continue-on-error` by design — build is the gate.
- `screenshot_aistudio` had `@google/genai@0.1.1` (never published) → bumped to 0.2.0; Gemini feature needs a runtime smoke test.
- Session GitHub token cannot create repos (403) or flip repo settings (Actions) — those are always user actions.
