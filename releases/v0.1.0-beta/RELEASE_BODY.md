# QAMAR Beta v0.1.0 (Windows 11 x64)

QAMAR is a local security and code-quality scanner. It runs nine well-known open-source engines on your own machine, combines their results, and says plainly
when something could not be checked. **Your source code never leaves your computer.** This is a **Beta**: it is not a security guarantee and not a compliance tool.

## Download and verify
| | |
|---|---|
| File | `qamar-0.1.0-beta-win-x64.zip` (294,670 bytes) |
| SHA-256 | `7cea0477e137db24eb256b908290f1722a6df8acb24e92c4ce928883ff2b9648` |
| Source commit | `f3fe50f36a` (development repository is private; the ZIP contains the CLI's JavaScript source under Apache-2.0) |
| Signed? | **No.** Neither the ZIP nor the scanner programs are code-signed. The SHA-256 is how you know you have the released file. |

Verify **before** unzipping (PowerShell, in the download folder); the result must equal the SHA-256 above:
```
Get-FileHash .\qamar-0.1.0-beta-win-x64.zip -Algorithm SHA256
```

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
