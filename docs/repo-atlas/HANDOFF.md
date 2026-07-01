# Repo Atlas — Session Handoff

> For the next Claude Code session. Read this + INVENTORY.md + STATUS.md in this
> directory to fully restore context. Last session ended 2026-06-30.

## State

- **Phase 1 (triage): done.** All 27 repos inventoried → `INVENTORY.md`. 27 → ~13–15 keepers; 12 flagged for archive (NOT yet archived).
- **Phase 3 (preview pipelines): done.** Tailored CI on branch `claude/repo-organization-artifacts-tnwgtb` in every keeper. 11/13 green → `STATUS.md` for the dashboard + per-repo caveats.
- **Atlas home:** user created `DirtyBananas/Atlas` as the dedicated index repo. The previous session could not reach it (repo scope fixed at session start).

## Next steps, in order

1. **Move the atlas** — copy `INVENTORY.md`, `STATUS.md`, and this file's successor from `everything-claude-code:docs/repo-atlas/` (branch `claude/repo-organization-artifacts-tnwgtb`) into `DirtyBananas/Atlas` with a README that makes Atlas the canonical dashboard. Optionally remove the copies here afterward.
2. **Confirm user toggles** (user does these; verify + update STATUS.md):
   - Pages → GitHub Actions on `og_iching_v2_emergent` → then re-run Web Preview → record the live URL in Atlas.
   - Actions enabled on `ruflo` + `everything-claude-code` → verify their pushed CI registers and runs.
3. **Phase 2 archiving** (needs explicit user greenlight, reversible): archive the 12 repos listed in INVENTORY.md (start with empty `iching2`, `iching3`, then the iching also-rans, `saph1`, `balconfig`, `snapxapp-emerge`, `privateephemeralbrowser`).
4. **Optional fix pending user decision:** `screencalltoaction` APK — bump `react-native` 0.79.5 → 0.81.5 (+ react/react-dom/native deps) in `frontend/package.json` to match Expo SDK 54's generated `MainApplication.kt` (`loadReactNative`). Its web build is already green.
5. **Merge pipeline branches to `main`** per keeper repo once the user is satisfied (workflows also trigger on main).

## Gotchas learned (don't relearn these)

- These AI-generated repos mostly have **no committed lockfiles** → never use setup-node `cache:` or `npm ci` without verifying the lockfile exists at that exact path.
- Expo web on Pages: patch `app.json` at build time only → `web.output="single"` (avoids SSR `window` crash) + `experiments.baseUrl="/<repo>"`.
- Committed `gradlew` files lack the exec bit → `chmod +x` before calling.
- electron-builder auto-publishes if a repo is configured → always `--publish never` in CI artifact builds.
- `skyline-estimator` lint (124 errors) and tests are `continue-on-error` by design — build is the gate.
- `screenshot_aistudio` had `@google/genai@0.1.1` (never published) → bumped to 0.2.0; Gemini feature needs a runtime smoke test.
- Session GitHub token cannot create repos (403) or flip repo settings (Pages/Actions) — those are always user actions.
