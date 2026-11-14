# Security model and scanner matrix (Beta v0.1.1)

Written to be accurate, not reassuring. "Verified" means it was run on Windows 11 x64 (see `RELEASE_NOTES.md`); "not tested" means it was not.

## 1. What QAMAR protects, and from whom
QAMAR scans folders you point it at. **An untrusted project** is any folder or Git repository whose author you do not control (a download, a pull request, a cloned repository).
The attacker is the project's author. Their goals: make QAMAR run a program of theirs, write outside QAMAR's folders, leak data, or **hide** a flaw so the scan looks clean.

Out of scope: an attacker who already runs code as your Windows user (they can edit QAMAR's folders, the consent file and stored results), a compromised upstream scanner release
whose official hash was published by the attacker, and malware that the scanned project's own build or tests would run (QAMAR never builds or tests your project).

## 2. What QAMAR does about it
| Risk | What QAMAR does | Status |
|---|---|---|
| Git runs programs named in the repository's own config (`core.fsmonitor`, clean/smudge filters, external diff, textconv, hooks, credential/pager/ssh commands) | All Git use goes through one layer (`scripts/lib/git-safe.mjs`): absolute `git` path outside the project, allow-listed environment, no system or user Git config, every program-running config key neutralised (fixed list **plus** dynamic discovery, including `include` files), repositories that move their work tree are refused, no submodule recursion, timeouts. Scanners that call Git themselves (Gitleaks) get the same environment. | Verified: a crafted repository executed a program with Beta 0.1.0 and does not with 0.1.1 (regression tests GS-1..6, including real Gitleaks) |
| `simplify --apply` rewriting the wrong file or losing it | Refuses untracked/modified files, links/junctions in the path, files that change while being checked, rewrites that touch other files; atomic restore; an on-disk recovery journal for interrupted runs (`qamar simplify <file> --restore`); your `--test` command is shown before it runs | Verified for failed tests, links/junctions and a killed process (GS-7..9). Power loss mid-write is **not** tested |
| A project hiding findings from the scan | Every scanner gets a neutral QAMAR-owned configuration; project config/ignore files and the inline suppressions that can be disabled are ignored (`nosemgrep`, `gitleaks:allow`, zizmor ignores); a project `.qamarignore` applies only with `--use-qamarignore`; default folder names never hide secrets; files above the size cap, `.gitleaksignore` and `trivy:ignore` comments (cannot be disabled) make the run **partial** unless you pass `--accept-skipped-large` / `--accept-project-suppressions`. The report lists every project-supplied config and suppression marker | Verified with real scanners and a hostile project (SP-1..6) |
| Tampered or malicious scanner download | Official GitHub release URLs only (https, host allow-list, redirects checked, size and time limits); SHA-256 of the download is checked against the pin shipped in the package **before** extraction; extracted tree is checked for links/special files/size/count; the installed executable is compared with a pinned hash from the package manifest before **every** run (a modified executable plus a modified receipt is still refused); atomic, locked install | Verified (TI-1..4 and a real install of two scanners). Executable pins exist for Windows x64 only; Linux pins are archive-level |
| Data leaving your machine | Source code never leaves it. Network use: scanner downloads (`tools install`), dependency lookups to OSV (package names and versions; avoid with `--offline`), and the optional feedback report you send yourself. An `--offline` scan makes no connection (verified through a loopback proxy, PV-1) | Verified |
| Secrets in results | Gitleaks values are redacted and dropped; other text goes through pattern redaction (covers common tokens, `user:password` in commands and URLs, Authorization headers). Redaction is pattern-based and **can miss unusual formats** | Partly verified |
| `--output` overwriting something else | Refuses links/junctions and non-regular files | Verified for directories; symlink case skipped where Windows denies creating one |

## 3. What QAMAR does not do
- It does not sandbox the scanner programs. They run as you, in a minimal environment, with the project as their working folder. A bug in a scanner could still be exploited by a crafted file. Treat scanning a hostile project like opening it in a parser, not in a VM.
- It does not execute the scanned project's code, build scripts or tests, and never takes a test command from the project. `simplify --apply --test` runs only the command **you** type.
- It does not defeat inline suppression comments that Biome or Trivy honour; it reports how many exist.
- Windows may still resolve helper programs by name; QAMAR sets `NoDefaultCurrentDirectoryInExePath` and starts Git by absolute path. Scanner-internal lookups are not audited.
- Consent and stored results live under `%LOCALAPPDATA%\qamar` with default Windows permissions; any process running as you can change them.

## 4. Scanner matrix (pinned in `tools/manifest.json`, Windows x64)
| Engine | Version | Licence | Source | Role | Project files it would read (QAMAR overrides) | Network by default | Tested here |
|---|---|---|---|---|---|---|---|
| OpenGrep | 1.33.1 | LGPL-2.1 | github.com/opengrep/opengrep | SAST with QAMAR rules | `.semgrepignore`, `nosemgrep` → disabled | none (version check off) | scans, hostile-ignore test |
| Gitleaks | 8.30.1 | MIT | github.com/gitleaks/gitleaks | secrets (working tree; history with `--deep`) | `.gitleaks.toml`, `gitleaks:allow` → neutral config and disabled; **`.gitleaksignore` is still honoured** → run is partial | none | scans, hostile-config and malicious-git tests |
| OSV-Scanner | 2.7.1 | Apache-2.0 | github.com/google/osv-scanner | dependency vulnerabilities | `osv-scanner.toml`, `.gitignore` → neutral config, `--no-ignore` | OSV.dev (names and versions) unless `--offline` | scans, offline test |
| zizmor | 1.32.0 | MIT | github.com/zizmorcore/zizmor | GitHub Actions security | `.github/zizmor.yml`, ignore comments → disabled | none (`--offline`) | scans, hostile-config test |
| Biome | 2.6.7 | Apache-2.0 OR MIT | github.com/biomejs/biome | JS/TS lint | QAMAR copies its own policy outside the project; **inline `biome-ignore` is honoured** (reported) | none | scans |
| Trivy | 0.76.2 | Apache-2.0 | github.com/aquasecurity/trivy | IaC/config misconfiguration | `trivy.yaml`, `.trivyignore` → neutral config; **inline `#trivy:ignore` is honoured** → run is partial | none (version check off) | scans, hostile-config and offline tests |
| actionlint | 1.7.12 | MIT | github.com/rhysd/actionlint | workflow correctness | `.github/actionlint.yaml` → neutral config | none | scans, hostile-config test |
| Syft | 1.57.1 | Apache-2.0 | github.com/anchore/syft | SBOM on `--sbom` | `.syft.yaml` → neutral config | none | one scan |
| ast-grep | 0.51.2 | MIT | github.com/ast-grep/ast-grep | `simplify` rewrites | fixed QAMAR config | none | simplify tests |

Not tested for any engine: very large archives inside a project, memory exhaustion by crafted input beyond the 10-minute per-engine limit and the 64 MB output limit, Linux, other Windows versions.

## 5. Verify a release
The SHA-256 of the ZIP is on the release page and in `SHA256SUMS`. In PowerShell: `Get-FileHash .\qamar-0.1.1-beta-win-x64.zip -Algorithm SHA256`. The package is not code-signed. `qamar-diagnose.cmd` re-checks every file against `CHECKSUMS.txt`.

## 6. Reporting a vulnerability
This Beta has no intake channel (see `FEEDBACK.md`). Do not publish exploit details for QAMAR itself; a private channel may be added in a later release.
