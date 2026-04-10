# Repository Guidelines

## Project Structure & Module Organization
This repository contains one LuCI package: `luci-app-3ginfo-lite/`.

- `luci-app-3ginfo-lite/Makefile`: OpenWrt package metadata (`PKG_VERSION`, `PKG_RELEASE`, deps).
- `luci-app-3ginfo-lite/htdocs/luci-static/resources/view/modem/*.js`: LuCI frontend views.
- `luci-app-3ginfo-lite/root/`: files installed on target routers (`/etc`, `/usr/share`, ACL/menu JSON).
- `luci-app-3ginfo-lite/po/`: translations (`template`, language-specific `.po` files).
- `.github/workflows/manual_release.yml`: CI/CD for IPK/APK package builds and release upload.

## Build, Test, and Development Commands
There is no standalone app runtime; validate changes by building the package in an OpenWrt SDK.

- `make package/luci-app-3ginfo-lite/clean`
  Cleans package build artifacts in SDK.
- `make package/luci-app-3ginfo-lite/compile -j$(nproc)`
  Compiles only this package (fast path).
- `make package/luci-app-3ginfo-lite/compile -j1`
  Fallback for clearer single-thread troubleshooting.
- `./scripts/feeds update packages luci && ./scripts/feeds install -a -p luci`
  Refreshes and installs LuCI feed dependencies in SDK.

## Coding Style & Naming Conventions
- Shell scripts: POSIX `sh` style, 2-4 space indentation, quote variables (`"$var"`).
- JavaScript LuCI views: keep functions small, avoid global side effects, follow existing file patterns in `3gconfig.js`, `3gdebug.js`, `3gdetail.js`.
- Keep package/install paths stable under `root/` to avoid upgrade regressions.
- Translation files should mirror existing keys; update `po/template/3ginfo.po` first when adding strings.

## Testing Guidelines
- Primary validation is package build success for both CI targets (IPK and APK).
- After UI/script changes, verify LuCI pages load and modem info scripts run on a test router.
- For CI edits, confirm artifact collection finds expected outputs (`*.ipk` and/or `*.apk`).

## Commit & Pull Request Guidelines
Current history uses short imperative subjects (for example: `Update manual_release.yml`, `Fix APK build ...`).

- Commit message format: `<Verb> <scope>` (example: `Fix modem detection for Quectel IDs`).
- Keep commits focused (package logic, translations, or workflow changes separately).
- PRs should include: change summary, why it is needed, CI impact, and relevant logs/screenshots for LuCI UI changes.
