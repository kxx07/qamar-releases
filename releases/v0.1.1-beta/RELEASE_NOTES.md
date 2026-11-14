# QAMAR Beta v0.1.1 — release notes (security release)

**Status:** public Beta, Windows 11 x64. Distributed as a portable ZIP (`qamar-0.1.1-beta-win-x64.zip`). Not code-signed. **Replaces Beta v0.1.0, which has a security flaw: please stop using it.**

## Why this release exists
An independent audit of Beta v0.1.0 proved that **scanning a Git repository you do not trust could run a program chosen by that repository's author** on your computer (through settings that Git reads from the repository itself when QAMAR asks it about the repository). It also showed that a scanned project could **hide findings** from QAMAR with its own configuration or ignore files and still get a "complete" scan. Both are fixed here. The fix is described in `SECURITY_MODEL.md`.

**If you scanned untrusted repositories with v0.1.0:** treat that computer as possibly affected only if the repository's author wanted that (no real-world abuse is known to us). Delete v0.1.0 and install this release. Scans of your own projects were not exposed.

## What changed
- **Git can no longer run programs from a scanned repository.** All Git use goes through one hardened layer: isolated environment, no system or user Git configuration, every program-running setting neutralised, repositories that redirect their work tree refused. The scanners that call Git themselves (Gitleaks) run in the same isolated environment. Tests prove a crafted repository runs nothing (including with the real Gitleaks), while the revision, dirty state and changed-file results stay correct.
- **A project cannot hide findings from the scan.** Each scanner now uses a neutral QAMAR configuration. Project config/ignore files and the inline suppressions that can be disabled are ignored; the report lists everything the project supplied (`Scope notes`). A project `.qamarignore` is applied only if you pass `--use-qamarignore`. Default folder names (`vendor`, `build`, `dist`, `data`...) no longer hide secrets. A scan is **partial** (exit code 3) when files above the size cap were skipped, when the project has a `.gitleaksignore` or `trivy:ignore` comments (the scanners honour them and QAMAR cannot turn that off) unless you acknowledge it with `--accept-skipped-large` / `--accept-project-suppressions`. Lockfiles that exist but yield no packages are an error, not "clean".
- **`simplify --apply` is safer:** refuses links/junctions, files that change while being checked, rewrites touching other files; restores atomically; keeps a recovery journal so an interrupted run can be undone with `qamar simplify <file> --restore`; shows your `--test` command before running it. `--preview` no longer involves the project's Git settings at all.
- **Scanner installation:** the pinned SHA-256 of every installed executable now comes from the package (not from a receipt beside it); downloads are limited to GitHub release hosts over https with size and time limits; extracted archives are checked for links, special files and size; installs are atomic and locked.
- **Privacy and network:** `--offline` scans open no connection at all (Trivy's version check is now off, verified through a loopback proxy); more credential patterns are redacted from stored text (`curl -u user:pass`, `https://user:pass@host`, Authorization headers); `--output` refuses to write through links.
- **`--changed`** no longer skips files with non-Latin names (it used to report "complete" without them).

## Verified for this release
- Windows 11 x64, Node.js 22: the project's automated tests (see the release page for the exact counts), the new security regression tests, a real install of scanners, and complete/partial/hostile-project scans with the real scanners. Details in `SECURITY_MODEL.md`.

## Not verified (see `KNOWN_LIMITATIONS.md`)
Linux and macOS, Windows 10/ARM64, antivirus interaction, power loss during a `simplify --apply` write, crafted inputs that exhaust memory inside a scanner, SonarQube/DefectDojo imports, the legal register, the feedback retention schedule (the maintainer deletes expired reports by hand at least monthly), and use by anyone other than the maintainers. **This release has not had the second independent AI code review**: the reviewer was unavailable; the changes were reviewed by an independent read-only agent and tested, which is not the same.

## Upgrade notes
Delete the old folder and extract this ZIP anywhere; stored results in `%LOCALAPPDATA%\qamar` are reused. New flags: `--use-qamarignore`, `--accept-skipped-large`, `--accept-project-suppressions`, `simplify --restore`. A scan that was "complete" in v0.1.0 may be "partial" now if your project has files over 2 MB, a `.gitleaksignore` or `trivy:ignore` comments: that is the point.
