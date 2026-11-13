# Known limitations (Beta v0.1.0)

Written to be accurate, not reassuring. "Verified" means it was run; "not verified" means it was not.

## Platform and installation
- **Windows 11 x64 is the only tested platform** (10.0.26300, Node.js 22.23.2). Windows 10, ARM64, Linux and macOS are **not tested and not supported**
  for this package.
- **Node.js 20+ must already be installed;** it is not bundled.
- The package and the scanner programs are **not code-signed**. Integrity comes from SHA-256 checks: you check the ZIP, QAMAR checks every
  scanner download against pinned values and refuses mismatches.
- First run needs internet access (about 0.6 GB on disk). There is no offline installer in this Beta.
- Antivirus products may flag or quarantine security scanners (not verified either way). QAMAR reports a missing engine as a partial scan.

## What a scan can and cannot tell you
- **No scanner proves that code is secure.** A scan without findings is not proof of safety.
- Findings are **scanner evidence, not proof of exploitability**, and some are false positives. QAMAR's own rules are intra-file only
  (no cross-file data flow). JavaScript, TypeScript and Python get QAMAR's code rules; other languages get secret, dependency and
  configuration checks only. Kotlin, Java, C#, Go, PHP and others have **no QAMAR code rules** in this Beta.
- A **partial** scan (exit code 3) is reported honestly: an engine failed, timed out, was missing, was offline without its database, or
  could not analyse some files. It is never shown as complete.
- `--changed` reports code, lint and secret findings only for files changed in git (the secret scanner still reads the whole folder, and `--deep` history findings are not
  filtered); dependency, workflow and configuration engines report on the whole project.
- Dependency vulnerabilities use the public OSV service (package names and versions only) unless you use `--offline` with a downloaded database.
- Git-history secret findings (`--deep`) show the commit; the secret value itself is never stored.
- Runtime behaviour, business logic and authentication design are **not** analysed.

## Experimental features
- `simplify`: suggested rewrites with an equivalence label. `--apply` edits the target file and refuses it unless that file is tracked and unmodified in a git work tree
  (other uncommitted files are not checked); it restores the file if your test command fails. Use it on a copy first.
- `export --to sonar|defectdojo`: writes **files** in those tools' import formats. No import into a running SonarQube or DefectDojo has been
  verified; do not describe it as an integration.
- `legal`: regulatory *applicability signals* from a register whose sources are "cited, not re-verified". **Not legal advice**, no compliance
  conclusion.
- `baseline` / `scan --baseline`: the security gate; new in this Beta, with a small tested rule set.

## Not included in this Beta
The web dashboard, the governance server, the Claude Code plugin and MCP tools. They exist in the project but are not part of this package and are
not supported here.

## Feedback and privacy limits
- Diagnostics are opt-in and manual (see `FEEDBACK.md`). Reports contain no paths, names or code, but **once sent they cannot be tied to you, so they
  cannot be individually looked up or deleted on request** (identical reports from different people look identical). The retention rule is 180
  days, enforced by an automated job whose manual runs were verified but whose daily schedule is **not yet observed** (it needs the maintainer's
  GitHub Actions minutes). Until it is confirmed, the maintainer applies the same rule by hand at least once a month (no report can reach 180 days
  before 2027-05-12). Until deleted, a report exists in a private repository.
- The feedback endpoint is a Cloudflare Worker. Cloudflare necessarily sees the connecting IP address as the network provider; QAMAR's Worker keeps only a
  salted hash for rate limiting, for the current UTC day.
- The default endpoint address contains the maintainer's workers.dev account name.
- **There is no support or free-text channel in this Beta** (no e-mail, no issue tracker), and no way to send installation problems when the CLI does not
  start: `qamar-diagnose.cmd` is a local self-check only.

## Other
- Reports and scan data live in `%LOCALAPPDATA%\qamar` in clear text and are readable by anyone with access to your Windows account. They contain
  file paths and messages from your projects. Do not share raw scan files without reviewing them.
- Exit codes and output formats may change before 1.0.
