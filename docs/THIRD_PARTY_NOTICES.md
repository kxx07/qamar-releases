# Third-party notices and dependency information

## What this package contains
Only QAMAR's own command-line code and rules (Apache-2.0, see `LICENSE`). **No third-party code or binaries are bundled and there are no npm
dependencies** (`package.json` has none).

## Prerequisite: Node.js
QAMAR runs on Node.js 20 or newer, which you install yourself from https://nodejs.org (MIT licence, with its own bundled-component notices). It is not
redistributed here.

## Scanner programs (downloaded on first run, not bundled)
`qamar tools install` downloads these exact releases from their upstream release pages into `%LOCALAPPDATA%\qamar\tools` and refuses any file whose
SHA-256 differs from the value below (pins for win32-x64; the manifest in `tools/manifest.json` also lists linux-x64 pins). QAMAR does not modify or
redistribute them; each remains under its own licence and is yours to obtain from upstream under that licence. OpenGrep is LGPL-2.1: it is downloaded
unmodified from its upstream project and not distributed by QAMAR.

| Tool | Version | Licence | Role | Download (upstream) | SHA-256 |
|---|---|---|---|---|---|
| opengrep | 1.33.1 | LGPL-2.1 | SAST (structural + taint) with QAMAR-authored rules | https://github.com/opengrep/opengrep/releases/download/v1.33.1/opengrep_windows_x86.exe | `132e606e071bdd3928286fddc893198a6106a90820d46e21197e0ace1a23e0c1` |
| ast-grep | 0.51.2 | MIT | AST structural rules and constrained rewrites | https://github.com/ast-grep/ast-grep/releases/download/0.51.2/app-x86_64-pc-windows-msvc.zip | `7d5a83bf2f9a4020dc529df371537c860e7a665e52789ac67ef231123a6b4a7e` |
| gitleaks | 8.30.1 | MIT | secret detection (redacted) | https://github.com/gitleaks/gitleaks/releases/download/v8.30.1/gitleaks_8.30.1_windows_x64.zip | `d29144deff3a68aa93ced33dddf84b7fdc26070add4aa0f4513094c8332afc4e` |
| osv-scanner | 2.7.1 | Apache-2.0 | dependency vulnerabilities (SCA) from lockfiles | https://github.com/google/osv-scanner/releases/download/v2.7.1/osv-scanner_windows_amd64.exe | `7121dcf420aefbbf89a7f0050c4d3094e079ff93afd334dfc96f16b02ec85800` |
| zizmor | 1.32.0 | MIT | GitHub Actions security | https://github.com/zizmorcore/zizmor/releases/download/v1.32.0/zizmor-x86_64-pc-windows-msvc.zip | `d284a16e09d9df4ce2c000357e7b59bd531be093bea5718a1ce225b449b59d49` |
| biome | 2.6.7 | Apache-2.0 OR MIT | JS/TS lint incl. cognitive complexity | https://github.com/biomejs/biome/releases/download/%40biomejs/biome%402.6.7/biome-win32-x64.exe | `36725369c94245889e9b40ee12e7740563dd2e98fd9b95a62cd986deb9ec132e` |
| trivy | 0.76.2 | Apache-2.0 | IaC/config misconfiguration (deep, only when IaC files exist) | https://github.com/aquasecurity/trivy/releases/download/v0.76.2/trivy_0.76.2_windows-64bit.zip | `691f737a7e5566a673c3a74dab9160552ec32101e953b659e5c77443d16dddb6` |
| syft | 1.57.1 | Apache-2.0 | SBOM (CycloneDX) on --sbom | https://github.com/anchore/syft/releases/download/v1.57.1/syft_1.57.1_windows_amd64.zip | `3a01fe865076bb548352195b77e88fa4649277861ea458efe165c3a810b6dcc7` |
| actionlint | 1.7.12 | MIT | workflow correctness (deep) | https://github.com/rhysd/actionlint/releases/download/v1.7.12/actionlint_1.7.12_windows_amd64.zip | `6e7241b51e6817ea6a047693d8e6fed13b31819c9a0dd6c5a726e1592d22f6e9` |

Licence names are as recorded in `tools/manifest.json` at build time and **must be re-verified against each project** before any redistribution.

## Services contacted
- GitHub release pages (scanner download, `tools install`).
- OSV (api.osv.dev): package names and versions only, unless you scan with `--offline`.
- The QAMAR feedback endpoint: only if you opt in and run `qamar feedback send` yourself (see `FEEDBACK.md`).
