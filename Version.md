# Version & Changelog History

## Project: Brave-Origin-Profile-MacOS
**Created:** 2026-09-28 02:19:00 IST
**Architecture:** POSIX / Zsh / Apple JXA (macOS Native Automation)
**Target Platform:** macOS (Darwin 10.15 Catalina through macOS 15 Sequoia / Apple Silicon & Intel)
**Primary Repository:** https://codeberg.org/mrdarksidetm/Brave-Origin-Profile-MacOS
**Mirror Repository:** https://gitlab.com/mrdarksidetm/Brave-Origin-Profile-MacOS

---

### [Initial Release] - 2026-09-28 02:19:00 IST
- **Author**: Antigravity Pair Programmer
- **Status**: Initialized & Verified (100%)
- **Target Channels**:
  - Brave-Origin (Release)
  - Brave-Origin-Beta (Beta)
  - Brave-Origin-Nightly (Nightly)
- **Libraries & Tools**:
  - macOS Native Shells: `/bin/zsh`, `/bin/sh` (POSIX compliant)
  - JavaScript for Automation (JXA): `osascript -l JavaScript` (JavaScriptCore Foundation bindings)
  - macOS Subsystem Utilities: `sw_vers`, `uname`, `hdiutil`, `ditto`, `plutil`, `curl`, `pkill`, `xattr`
  - CI Engines: GitLab CI (`alpine:latest`), GitHub Actions (`macos-latest`)
- **Modules & Files Created**:
  - `README.md` — Complete project overview, dual-host Quick Run one-liners, CLI parameters table, native architecture documentation, legal shield analysis, and mirror links.
  - `LICENSE` — MIT License for open-source distribution.
  - `LEGAL.md` — In-depth statutory defense, fair use, first-sale doctrine, and non-circumvention documentation.
  - `.gitignore` — macOS-specific file patterns (`.DS_Store`, resource forks, spotlight indexes, backup files).
  - `.gitlab-ci.yml` — Automated GitLab CI pipeline executing syntax verification (`bash -n`) on `scripts/profile.sh`.
  - `.github/workflows/validate.yml` — GitHub Actions automated syntax and lint validation workflow on `macos-latest`.
  - `scripts/profile.sh` — 100% zero-dependency native macOS profile manager and offline state utility. Features:
    - `detect_environment`: Queries `sw_vers` and `uname -m`, with Apple Silicon (`arm64`), Rosetta 2 translation, and Intel (`x86_64`) detection.
    - `install_brave_origin`: Downloads official DMG from Brave CDN matching host architecture, mounts via `hdiutil attach -nobrowse -readonly`, copies `.app` via `ditto` to `/Applications` (or `~/Applications`), removes quarantine with `xattr -cr`, and cleanly detaches and purges temporary files.
    - `patch_local_state`: Atomic state mutator using `osascript -l JavaScript` and Cocoa `ObjC.import('Foundation')` to read, mutate `brave.origin` and `skus.state`, and atomically write UTF-8 JSON without BOM corruption; validated with `plutil -lint`.
    - `restore_local_state`: Instant configuration rollback from `.bak` backup file.
    - `stop_running_brave`: Detects running Brave Origin processes with interactive prompt or `--force` termination.
    - CLI parameter handling for `-c/--channel`, `-p/--path`, `-i/--install`, `-f/--force`, `-r/--restore`, `--no-backup`, and `-h/--help`.
  - `assests/` — High-resolution light and dark vector badges and icons.
  - `Version.md` — Project version and changelog source of truth.
