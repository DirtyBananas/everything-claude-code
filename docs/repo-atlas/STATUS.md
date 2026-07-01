# Repo Atlas — Preview Pipeline Status

> Status after fanning out tailored CI/preview pipelines to the keeper repos and a corrective pass.
> All pipelines live on branch `claude/repo-organization-artifacts-tnwgtb` in each repo.
> Last updated: 2026-07-01.

## Privacy decision (2026-07-01)

Repos stay **private**. GitHub Pages from a private repo publishes a *publicly accessible*
site (access-controlled Pages is Enterprise-only), so **no Pages deploys anywhere**.
The web preview tier = a downloadable `dist` artifact (`npx serve` it locally).
For a live URL without exposing code, use Netlify/Vercel's private-repo git integration
(`og_iching_v2_emergent` already has `netlify.toml`).

## Legend
- ✅ green — pipeline runs and produces its artifact/build
- 🔴 real app bug — needs a code/dependency decision (not a CI tweak)
- ⚠️ Actions disabled at repo level — needs you to enable it

## Dashboard

| Repo | Type | Pipeline(s) | Status | Output |
|---|---|---|---|---|
| og_iching_v2_emergent | Expo | Android APK | ✅ | `og_iching_v2_emergent-debug-apk` (~70 MB) |
| og_iching_v2_emergent | Expo | Web Preview | ✅ | `web-preview-dist` artifact (download + `npx serve . -s`) |
| og_iching_v2_emergent | Expo | Emulator (manual) | ✅ | screenshot artifact on dispatch |
| holaos | Electron | Installer (Windows) | ✅ | NSIS `.exe` artifact |
| snapxapp | Electron | Installer (Windows) | ✅ | NSIS `.exe` artifact |
| skyline-estimator | Electron | CI + Installer (Linux) | ✅✅ | build check + AppImage artifact |
| brunnfeld-agentic-world | Web | Web build artifact | ✅ | viewer build |
| product0 | Web (Next) | Build artifact | ✅ | `.next` build (Prisma generate + placeholder DB) |
| screenshot_aistudio | Web (Vite) | Build artifact | ✅ | `dist` build |
| skyconfigurator1 | Web (craco) | Build artifact | ✅ | frontend build (Node 22) |
| screencalltoaction | Expo | Web build | ✅ | web bundle |
| screencalltoaction | Expo | Android APK | 🔴 | RN version mismatch — see below |
| steward | Service | Web dashboard build | ✅ | `dist/web` (pnpm workspace) |
| recursive-improve | Python lib | CI | ✅ | build/test check |
| ruflo | Node CLI | (pushed) | ⚠️ | **Actions disabled** — enable to run |
| everything-claude-code | Node lib | CI (pushed) | ⚠️ | **Actions disabled** — enable to run |

## Your manual to-do list (things the API cannot do)

1. **Enable Actions** on `ruflo` and `everything-claude-code` → Settings → Actions → General → Allow. The workflow files are already pushed; they’ll register and run once enabled.
2. **`screencalltoaction` APK decision** — `frontend/package.json` pins `react-native@0.79.5`, but Expo SDK 54 prebuild emits the RN 0.81 entrypoint (`ReactNativeApplicationEntryPoint.loadReactNative`), failing Kotlin compile. Fix = bump `react-native` to 0.81.5 (and likely `react`/`react-dom`/native deps) to match the Expo SDK. This risks a dependency cascade, so it’s a deliberate call. (Its **web** build is already green.)
3. **Optional live URLs, privately:** connect keeper web repos to Netlify/Vercel (free tiers deploy from private repos; only the rendered site is public).
4. **Merge** the `claude/repo-organization-artifacts-tnwgtb` branch to `main` in each repo when satisfied — the pipelines also trigger on `main`, so merging activates them there.

## Notes & caveats captured during the fan-out

- **Recurring root cause** across 6 repos: `actions/setup-node` `cache:` pointed at a lockfile these AI-generated repos never committed. Fix pattern: drop `cache:` + use `npm install` (not `npm ci`) when no lockfile exists.
- **Stacked failures** uncovered while getting to green (each a real pre-existing issue, now fixed in CI or noted):
  - `snapxapp`: unused-import TS error, missing `tailwindcss-animate` dep, wrong Electron entry path, electron-builder publish → `--publish never`.
  - `product0`: raised tsconfig `target` to es2017 (a `Set` spread needed it).
  - `screenshot_aistudio`: `@google/genai@0.1.1` was never published → bumped to `0.2.0` (⚠️ not type-checked; smoke-test the Gemini feature).
  - `skyline-estimator`: lint made non-blocking (124 pre-existing `no-explicit-any` errors remain as debt); added `canvas` native build deps incl. `libpixman-1-dev`.
  - `holaos` / `snapxapp`: electron-builder tried to publish a Release without a token → `--publish never` (we want an artifact, not a release).
- **No lockfiles committed** in several repos → installs aren’t reproducible (resolve fresh from `^` ranges each run). Commit a lockfile + switch back to `npm ci` for reproducibility.
- Workflows currently pin `actions/*@v4` on Node 20; GitHub warns Node 20 is deprecated (non-fatal).
