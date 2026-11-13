# QAMAR Beta v0.1.0 — release notes

**Status:** public Beta, Windows 11 x64. Distributed as a portable ZIP (`qamar-0.1.0-beta-win-x64.zip`). Not code-signed.

## What is in it
A command-line security and code-quality scanner that orchestrates nine pinned open-source engines and reports honestly:
- **Engines:** OpenGrep (QAMAR's own rules), Gitleaks, OSV-Scanner, zizmor, Biome, Trivy, actionlint, Syft (SBOM on request) and ast-grep
  (for `simplify`). They are downloaded on first run and verified against pinned SHA-256 values; nothing is bundled.
- **Honest results:** a missing, failed, timed-out or offline engine, or files an engine could not analyse, make the run **partial** (exit code 3);
  nothing is reported as fixed unless the same engine covered the same file again; excluded paths are never reported as fixed.
- **Reports:** text, JSON, SARIF 2.1.0; comparison with the previous comparable scan; optional SBOM; SonarQube / DefectDojo import **files** (experimental).
- **Gate (new, experimental):** `scan --baseline` and `baseline create|check` fail only on findings a reviewed, expiring baseline does not cover.
- **Optional diagnostics:** `consent`, `feedback preview|send`: opt-in, manual, previewed, anonymous (see `FEEDBACK.md`).

## What was verified for this Beta
- The packaged ZIP itself was extracted to a path containing spaces and run on Windows 11 x64 with Node.js 22.23.2: `--version`, `doctor` with no tools
  (reported as unavailable, exit 3), `tools install` from the real upstream URLs with hash verification, `doctor` (nine of nine), complete and partial
  scans, JSON/SARIF output, the gate, the diagnostics script, and an opt-in feedback report (to a local test receiver). The release page lists the exact results.
- The project's automated test suite passes locally with no failures, including tests that run the real scanners; independent AI code review (OpenAI Codex) was run on the release changes.
- The ZIP is byte-reproducible: rebuilding it from a fresh checkout of the recorded source commit gives the same SHA-256.

## What was not verified (see `KNOWN_LIMITATIONS.md`)
Windows 10 / ARM64 / Linux / macOS for this package; a clean machine without any developer tools besides Node.js; antivirus interaction; SonarQube and
DefectDojo imports; the legal register's contents; the daily schedule of the feedback retention job (until confirmed, the maintainer applies the 180-day rule by hand at least monthly); use by anyone other than the maintainers.

## Changes made specifically for this Beta
- `qamar --version` (also prints your platform and scanner-pin platform).
- SARIF output no longer embeds a repository URL; a schema help text no longer names a private project.
- A portable package builder (`scripts/build-beta.mjs`) with a self-check (checksums, forbidden files, personal-data patterns) and byte-reproducible output: rebuilding the recorded commit gives the same SHA-256.
- Included from earlier work (merged for this release): the security gate, weekly scanner-advisory monitoring, evidence provenance, Linux scanner pins.

## Upgrade notes
First public Beta; no upgrade path yet. Scan results are stored in `%LOCALAPPDATA%\qamar` and may be incompatible between Beta versions.
