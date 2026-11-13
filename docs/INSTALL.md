# Installing and first run

## Requirements
- **Windows 11 x64** is the only tested platform (build 10.0.26300). Windows 10 may work but is untested and unsupported; ARM64 Windows, Linux and macOS are not supported by this package. About 1 GB of free disk space, and internet access for the first-run download.
- **Node.js 20 or newer.** Check with `node --version` in a new terminal. If it is missing or older, install the LTS version from
  https://nodejs.org . No administrator rights are needed to run QAMAR.
- `git` is optional but needed for `--changed`, history scanning (`--deep`) and revision information in reports.

## Verify the package (recommended)
The release came with a `.sha256` file. In PowerShell, in the folder containing the ZIP:
```
Get-FileHash .\qamar-0.1.0-beta-win-x64.zip -Algorithm SHA256
```
The value must equal the one in the `.sha256` file and in the message you received it with. The ZIP is **not code-signed**; the checksum
is how you know it is the file that was released. Inside the package, `CHECKSUMS.txt` lists the SHA-256 of every file;
`qamar-diagnose.cmd` re-checks them.

## Unzip
Right-click the ZIP and choose **Extract All** in Explorer, or run `tar -xf qamar-0.1.0-beta-win-x64.zip` (Windows 10/11 include `tar`), or use 7-Zip. On some machines
Windows PowerShell 5.1's `Expand-Archive` fails with "Cannot find an overload for ExtractToDirectory" for **any** ZIP file; that is a PowerShell/module problem
on that computer, not a damaged download: use one of the other methods. Extract to a normal folder (spaces in the folder name are fine).

## First run
```
.\qamar.cmd --version        # prints the version and your platform
.\qamar.cmd tools install    # downloads and verifies the nine scanners (one time)
.\qamar.cmd doctor           # every tool should show a green check
.\qamar.cmd scan .           # scan the current folder
```
- Everything QAMAR stores goes to `%LOCALAPPDATA%\qamar` (scanner programs, stored scan results, the offline vulnerability database,
  your consent choice). Nothing is written into the folder you scan, except what you explicitly ask for (`--output`).
- On the first interactive run QAMAR asks, once, whether you want to help improve it by sending anonymous diagnostics. **Answering no
  (or not answering) changes nothing**; QAMAR works fully without it and never sends anything automatically.
- To uninstall: delete the QAMAR folder and `%LOCALAPPDATA%\qamar`.

## Scanning without sending anything to the internet
Dependency checks (OSV) normally send **package names and versions only** (never source code) to the OSV service. To avoid even that:
run `.\qamar.cmd scan . --prepare-offline` once (downloads a ~0.2 GB vulnerability database), then use `--offline`. If the database is
missing, an offline scan reports the dependency check as *not done* and the run is **partial**.

## Troubleshooting
| Symptom | What it means / what to do |
|---|---|
| `QAMAR needs Node.js 20 or newer` | Install Node.js LTS from https://nodejs.org and open a **new** terminal |
| `tools install` fails: `SHA-256 mismatch` | The download was corrupted or altered; QAMAR refused that file and installed nothing for that tool (other tools may already be installed: `doctor` shows which). Retry once; if it repeats, stop and run `qamar-diagnose.cmd` (do not work around it) |
| `tools install` fails with `HTTP 403/429` or a network error | GitHub rate limit, proxy, or no internet. Wait and retry; set `HTTPS_PROXY` if you use a proxy |
| `doctor` shows a red entry | That scanner is missing or its file changed since installation: run `.\qamar.cmd tools install` again |
| A scan ends with `Partial scan` and exit code 3 | An engine failed, timed out, was missing, or could not read some files. The report names it. This is reported on purpose: do not treat a partial scan as clean |
| Antivirus blocks or deletes a scanner file | Some security programs flag security tools. QAMAR will then report the engine as missing (partial scan). Restore/allow the file from the vendor's official release only after checking its hash with `doctor` |
| `error: path not found` / usage errors | Exit code 2: check the command; `.\qamar.cmd` with no arguments prints the help |
| Long paths or very large projects | Windows path limit (260) can hide files; the report lists skipped counts. A scan has a 10-minute limit per engine (`--timeout-sec`) |
| Nothing works and you cannot tell why | Run `qamar-diagnose.cmd`: it writes `qamar-diagnostics.txt` (no user name, no paths, no project data). Read it: it shows a missing or old Node.js, a damaged or modified package, or an unsupported Windows build. It stays on your computer; this Beta has no support channel (see `FEEDBACK.md`) |
