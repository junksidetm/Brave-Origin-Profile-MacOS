<div align="center"> 
<picture>
<img
  width="128px"
  src="assests/images/Brave-origin-Profile-Light.svg"/>
</picture>

# Brave Origin Profile Manager & Offline State Utility for macOS
**Because a $60 paywall for a stripped-down browser on macOS that is literally free on Linux is absurd.**

<p>
  <a href="https://github.com/junksidetm/Brave-Origin-Profile-MacOS"><img src="https://img.shields.io/badge/GitHub-Main-181717?logo=github&logoColor=white" alt="GitHub Main" /></a>
  <a href="https://codeberg.org/mrdarksidetm/Brave-Origin-Profile-MacOS"><img src="https://img.shields.io/badge/Codeberg-Mirror-2185d0?logo=codeberg&logoColor=white" alt="Codeberg" /></a>
  <a href="https://gitlab.com/mrdarksidetm/Brave-Origin-Profile-MacOS"><img src="https://img.shields.io/badge/GitLab-Mirror-fc6d26?logo=gitlab&logoColor=white" alt="GitLab" /></a>
  <img src="https://img.shields.io/badge/macOS-10.15%20to%2015%2B%20%7C%20Apple%20Silicon%20%26%20Intel-black?logo=apple&logoColor=white" alt="macOS" />
  <img src="https://img.shields.io/badge/Runtime-Zero%20Dependencies%20(Native%20JXA)-success" alt="Zero Dependencies" />
  <img src="https://img.shields.io/badge/License-MIT-green" alt="MIT License" />
</p>

<p>
  <a href="#-quick-run-one-liners">Quick Run</a> •
  <a href="#-cli-parameters--flags">CLI Options</a> •
  <a href="#-how-it-works-native-macos-architecture">Native Architecture</a> •
  <a href="#-dual-mirror-git-hosts-codeberg--gitlab">Mirrors</a> •
  <a href="#-statutory-defense--legal-loopholes">Legal Shield</a> •
  <a href="LEGAL.md">LEGAL.md</a>
</p>

<sub><b>Nominative Fair Use Disclaimer:</b> "Brave" and "Brave Origin" are registered trademarks of Brave Software Inc. Any logos, modified badge assets, or brand names are used strictly for descriptive identification and interoperability purposes under Nominative Fair Use (15 U.S.C. § 1125). This project is independent and is not affiliated with, endorsed by, or sponsored by Brave Software Inc.</sub>
</div>

---

### What is this?
Brave charges a $60 buyout for "Brave Origin" on macOS (a clean Brave build stripped of crypto wallets, VPN promos, and AI bloat). Meanwhile, the exact same build is completely free on Linux.

The punchline? The macOS paywall check is literally a client-side flag (`purchase_validated: true`) stored inside a local plaintext JSON file (`Local State`). 

This script is an open-source **local configuration & profile state manager for macOS**. It flips that flag, injects the SKU credentials, preserves your existing profile data without corruption, and runs 100% offline. 

**Zero External Dependencies:**
- ❌ No Deno required
- ❌ No Node.js required
- ❌ No Python required (avoids macOS Monterey+ developer tools prompt)
- ❌ No Homebrew or Xcode Command Line Tools needed
- ✅ Pure native macOS: `/bin/zsh`, `/bin/sh`, `osascript` (JXA Foundation bindings), `hdiutil`, `ditto`, and `plutil`.

---

## ⚡ Quick Run (One-Liners)

Open any Terminal on macOS (Apple Silicon M1/M2/M3/M4 or Intel) and execute via your preferred mirror:

### 🏔️ Option A: Codeberg (Primary / EU / Forgejo)
```bash
bash <(curl -fsSL https://codeberg.org/mrdarksidetm/Brave-Origin-Profile-MacOS/raw/branch/main/scripts/profile.sh)
```

### 🦊 Option B: GitLab (Mirror)
```bash
bash <(curl -fsSL https://gitlab.com/mrdarksidetm/Brave-Origin-Profile-MacOS/-/raw/main/scripts/profile.sh)
```

*Auto-detects installed channels (Release, Beta, Nightly), backs up your config to `.bak`, closes locked browser processes cleanly, and applies the configuration patch. Both mirrors provide identical, signed code.*

---

## 🛠️ CLI Parameters & Flags

Want more control than blind one-click execution? Run `profile.sh` with dedicated switches:

```bash
./scripts/profile.sh [-c <Release|Beta|Nightly|All>] [-f] [-i] [-p <path>] [-r] [--no-backup]
```

### Parameter Specification & Script Flags
```bash
# Supported Flags & Switches:
-c, --channel <channel>     # Release, Beta, Nightly, or All (Default: "All")
-p, --path, --user-data-dir # Custom path to User Data directory or Local State (Default: empty)
-i, --install               # Download, mount DMG via hdiutil, and install if missing (Default: 0)
-f, --force                 # Terminate running Brave instances automatically without prompt (Default: 0)
-r, --restore               # Rollback to original Local State from .bak backup (Default: 0)
--no-backup                 # Skip writing .bak backup files (Default: 0)
-h, --help                  # Display help manual and exit
```

| Flag | Argument | Default | What it does |
| :--- | :--- | :--- | :--- |
| **`-c, --channel`** | `Release\|Beta\|Nightly\|All` | `All` | Target specific release channels (`Brave-Origin`, `Brave-Origin-Beta`, `Brave-Origin-Nightly`). Won't create dummy directories for channels not installed. |
| **`-f, --force`** | None | `Disabled` | Automatically terminates running Brave Origin instances without an interactive prompt. |
| **`-i, --install`** | None | `Disabled` | No Brave Origin found? Pulls official Apple Silicon or Intel `.dmg` from Brave's CDN, mounts via `hdiutil`, deploys to `/Applications` via `ditto`, and clears Gatekeeper quarantine. |
| **`-p, --path`** | `<path>` | `None` | Custom User Data directory path or direct path to a custom `Local State` file. |
| **`-r, --restore`** | None | `Disabled` | Instant rollback. Restores original `Local State` from `.bak` backup files if you ever want to revert. |
| **`--no-backup`** | None | `Disabled` | Skips writing `.bak` files before applying modifications. |

---

### Real-World Examples

```bash
# Standard: Detect whatever Brave Origin channels you have installed and configure them
./scripts/profile.sh

# The "Just do it": Kill active browser instances automatically and patch
./scripts/profile.sh -f

# Rollback: Revert everything back to how it was before running the patch
./scripts/profile.sh -r

# Missing the browser? Download DMG, mount, install, and configure in one command
./scripts/profile.sh -i -f

# Target specific Beta channel
./scripts/profile.sh -c Beta
```

---

## 🍏 How It Works: Native macOS Architecture

Existing utilities (like Deno-based scripts) require installing third-party runtimes. This tool eliminates all prerequisites by leveraging macOS system primitives:

1. **Architecture & Environment Detection (`sw_vers`, `uname -m`)**:
   Automatically detects Apple Silicon (`arm64`), Rosetta 2 translation (`sysctl.proc_translated`), or Intel (`x86_64`) to fetch the exact binary and configure the right application bundles.
2. **Zero-Download DMG Deployment (`hdiutil`, `ditto`, `xattr`)**:
   Downloads official DMGs via native `curl`, mounts silently via `hdiutil attach -nobrowse -readonly`, stages `.app` bundles cleanly to `/Applications` via Apple `ditto` (preserving code signatures and resource forks), and strips Gatekeeper quarantine flags via `xattr -cr`.
3. **Atomic JSON Mutation via JXA (`osascript -l JavaScript`)**:
   Uses Apple's native JavaScriptCore Foundation bridge (`ObjC.import('Foundation')`) to safely read and mutate `Local State`, then writes the updated JSON atomically with `writeToFileAtomicallyEncodingError` without BOM corruption.
4. **Validation via `plutil`**:
   Verifies syntax integrity with macOS Property List Utility (`plutil -lint`) after writing.

---

## 🛡️ Statutory Defense & Legal Loopholes

1. **Dual-Use Doctrine (*Sony Betamax* Defense)**:
   Under *Sony Corp. v. Universal City Studios* (1984), a tool cannot be outlawed if it is capable of **"substantial non-infringing uses"**. This utility serves as an offline profile state manager for sysadmins deploying offline enterprise seats, developers simulating testing environments, and portable USB profile users.
2. **First Sale Doctrine & Hardware Autonomy (17 U.S.C. § 109)**:
   You own your Mac and the local storage on your drive. Modifying an unencrypted plaintext JSON configuration file stored in your own `~/Library/Application Support/` directory is an exercise of user data sovereignty.
3. **Interoperability & Reverse Engineering (17 U.S.C. § 1201(f))**:
   Section 1201(f) explicitly protects analyzing and configuring data formats for software interoperability.
4. **No Digital Rights Management (DRM) Circumvention**:
   Plaintext JSON files are not "effective technological protection measures" under DMCA § 1201. Setting a boolean key inside an unencrypted JSON object violates no cryptographic protection.

See [`LEGAL.md`](LEGAL.md) for full statutory analysis.

---

## 🌐 Source Mirrors: GitHub, Codeberg & GitLab
 
This project is hosted symmetrically across independent git forges:

| Provider | Mirror URL | Type |
| :--- | :--- | :--- |
| **GitHub** | [github.com/junksidetm/Brave-Origin-Profile-MacOS](https://github.com/junksidetm/Brave-Origin-Profile-MacOS) | Main Repository |
| **Codeberg** | [codeberg.org/mrdarksidetm/Brave-Origin-Profile-MacOS](https://codeberg.org/mrdarksidetm/Brave-Origin-Profile-MacOS) | Mirror (Forgejo) |
| **GitLab** | [gitlab.com/mrdarksidetm/Brave-Origin-Profile-MacOS](https://gitlab.com/mrdarksidetm/Brave-Origin-Profile-MacOS) | Mirror (GitLab) |

```bash
# Clone from GitHub:
git clone https://github.com/junksidetm/Brave-Origin-Profile-MacOS.git

# Clone from Codeberg:
git clone https://codeberg.org/mrdarksidetm/Brave-Origin-Profile-MacOS.git

# Clone from GitLab:
git clone https://gitlab.com/mrdarksidetm/Brave-Origin-Profile-MacOS.git
```

---

## License

Released under the [MIT License](LICENSE).

---

<div align="center">
<a href="https://github.com/junksidetm/junksidetm.github.io">
  <img src="https://raw.githubusercontent.com/junksidetm/assests/981a029b9f59b8ed581bbee9be318c86da52dcca/Images/Codium/Codium%20Banner/SVG/Codium%20-%20Banner%20Black.svg" width="360"></a>
<br><br>
<sub>
<p>This repository is a part of `"Codeium"`. A part of Darkside Studio.
</p>
</sub>
<p><b><sub>© 2026 Abhijeet Yadav.  All rights reserved. All logos are Copyright Law </sub></b></p>
