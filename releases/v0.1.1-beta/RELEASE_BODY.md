# QAMAR Beta v0.1.1 (Windows 11 x64)

> **Security release.** Beta v0.1.0 had a flaw: scanning a Git repository you did not create could run a program chosen by its author, and a scanned project could hide findings. **Stop using v0.1.0 and upgrade.** See [the advisory](releases/v0.1.0-beta/SECURITY_ADVISORY.md) and [docs/SECURITY_MODEL.md](docs/SECURITY_MODEL.md). This release has not had the second independent AI code review (the reviewer was unavailable); it was reviewed by an independent read-only agent and tested, which is not the same.

QAMAR is a local security and code-quality scanner. It runs nine well-known open-source engines on your own machine, combines their results, and says plainly
when something could not be checked. **Your source code never leaves your computer.** This is a **Beta**: it is not a security guarantee and not a compliance tool.

## Download and verify
| | |
|---|---|
| File | `qamar-0.1.1-beta-win-x64.zip` (344,126 bytes) |
| SHA-256 | `f36c0d3782b0c9fecf5f1bd4ecab351cf4f38ef0b1392e25682e27becbf09ebd` |
| Source commit | `03e8f79c5a` (development repository is private; the ZIP contains the CLI's JavaScript source under Apache-2.0) |
| Signed? | **No.** Neither the ZIP nor the scanner programs are code-signed. The SHA-256 is how you know you have the released file. |

Verify **before** unzipping (PowerShell, in the download folder); the result must equal the SHA-256 above:
```
Get-FileHash .\qamar-0.1.1-beta-win-x64.zip -Algorithm SHA256
```

## What this release does not claim
- **It is not free of vulnerabilities.** It fixes the two flaws above and the other issues listed in the release notes; further flaws may exist.
- **Linux and macOS were not tested at all** (no Linux run of any kind, including the automated tests). Only Windows 11 x64 was tested.
- **The external scanner programs are not sandboxed.** They run as you, with the scanned project as their working folder. Treat scanning a hostile project like parsing its files, not like running it in a virtual machine.
- The project's required automated checks (GitHub Actions) could not run for this release because of a billing limit on the development repository; the tests were run locally instead. This release has not had a second independent code review.

## Requirements
- **Windows 11 x64** (the only tested platform: build 10.0.26300). Windows 10 is untested and unsupported; ARM64 Windows, Linux and macOS are not supported by this package.
- **Node.js 20 or newer**, installed by you from https://nodejs.org (not included).
- Internet for the first run (about 0.6 GB of scanner programs, downloaded from their official release pages and checked against pinned SHA-256 values; nothing is bundled).

## Install and first scan
Unzip (Explorer: Extract All), open a terminal in the folder, then:
```
.\qamar.cmd --version
.\qamar.cmd tools install
.\qamar.cmd doctor
.\qamar.cmd scan C:\path\to\your\project
```
A scan ends **complete** or **partial** (exit code 3: an engine failed or was missing); a partial scan is never a clean bill of health. `INSTALL.md` in the ZIP has the
full guide and a troubleshooting table.

## What was verified for this release
The exact ZIP was extracted into a folder with a space in its name on Windows 11 x64 (Node.js 22.23.2) and run end to end: version and usage errors, the "no Node.js" message,
a fresh machine (no tools: reported unavailable, scan **partial**), real scanner installation with hash verification (nine of nine), a complete scan, offline-without-database
**partial**, the optional gate, option validation, the diagnostics script, and opt-in feedback to a local test receiver. The ZIP is byte-reproducible from its source commit.

## Experimental (use, but do not rely on)
`simplify` (`--apply` edits files), `export` (SonarQube / DefectDojo import **files**; no import into a running server verified), `legal` (regulatory applicability signals from a
register that is "cited, not re-verified"; **not legal advice**), and the baseline gate.

## Privacy and feedback
Nothing is sent unless you opt in (`qamar consent grant`) **and** confirm each report (`qamar feedback send` shows the exact payload first; default answer is No). A report contains
only: format and consent versions, QAMAR version, `windows`, Node.js major version, the command (always `scan`), whether your latest scan was complete or partial (exit code 0 or 3), per-engine status,
an optional error code and a duration bucket. Never code, paths, names, findings, messages or credentials. Reports go to a private repository and are meant to be deleted after 180 days: the automated job has **not yet been
observed running**, so the maintainer applies the rule by hand at least monthly until it is. Anonymous reports cannot be deleted per person on request.
**There is no e-mail address, issue tracker or support channel in this Beta**, and no way to send installation problems when the CLI does not start.

## Known limitations
No scanner proves code is secure; QAMAR's own rules are intra-file and cover JavaScript, TypeScript and Python code (other languages get secret, dependency and configuration checks
only); antivirus interaction is unverified; the dashboard, governance server and plugin are not part of this package. See `KNOWN_LIMITATIONS.md` in the ZIP.

## Licence
QAMAR's own code and rules: Apache-2.0. The scanners are separate programs under their own licences (OpenGrep LGPL-2.1; Gitleaks, zizmor, ast-grep, actionlint MIT; OSV-Scanner,
Biome, Trivy, Syft Apache-2.0 or dual-licensed), downloaded unmodified from upstream on your machine and not redistributed here: see `THIRD_PARTY_NOTICES.md`.
