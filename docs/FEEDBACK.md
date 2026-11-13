# Feedback and privacy

You can use QAMAR without ever sending anything. Feedback is optional, anonymous and manual.

## 1. What this Beta can and cannot receive
The **only** feedback channel in this Beta is the optional, automated, anonymous diagnostic report described below. There is **no e-mail address, no
issue tracker and no free-text form** (a deliberate, temporary choice). So reports cannot contain opinions, screenshots or error text, and they
**cannot be answered**.

What helps most: after a scan, especially one that ended **partial** or with an error, run `qamar feedback preview` and, if you are comfortable with
what it shows, `qamar feedback send` (steps below). A report is built from your most recent stored **scan** and tells us whether it finished complete or partial and which scanner engines worked.

## 2. If QAMAR cannot even start
Run `qamar-diagnose.cmd` (double-click or run in a terminal). It needs **no Node.js and no QAMAR**. It writes `qamar-diagnostics.txt` next to it:
Windows version, CPU architecture, whether Node.js is installed and its version, and whether every file of this package still
matches `CHECKSUMS.txt` (package-relative file names only; if that file itself was modified, it is not used and the report says so). It contains **no user name, no folder paths and no project data**.
**This file stays on your computer: nothing sends it anywhere, and this Beta has no way to receive it.** It is for you: it tells you whether Node.js
is missing or too old, whether the download is damaged or modified, and what Windows build you are on. `INSTALL.md` has a troubleshooting table.

## 3. Anonymous diagnostics from the CLI (optional)
```
.\qamar.cmd consent status
.\qamar.cmd consent grant           # you opt in; nothing is sent yet
.\qamar.cmd feedback preview        # shows the exact report, nothing is sent (needs a stored scan)
.\qamar.cmd feedback send           # shows the destination and the exact report, asks "Send this report? [y/N]" (default No)
.\qamar.cmd consent revoke          # opt out at any time
```
Nothing is ever sent without **both** your consent and your confirmation of each report. Without a terminal, `send` refuses unless you add `--yes`
(which skips only the question, never the consent or the validation). QAMAR also asks once, in an interactive terminal, whether you want to opt in;
answering no changes nothing.

**What a report contains (complete list):** report format version, consent text version, QAMAR version, operating-system family (`windows`), Node.js
major version, the command (always `scan` in this Beta), the outcome (`ok` for a complete scan, `partial` otherwise) with the matching exit code (0 or 3: the
report does not carry your scan's findings exit code), the status of each scanner
engine (ok / failed / timeout / missing...), an optional upper-case error code such as `LOCK_TIMEOUT`, and a duration bucket (for example `10-60s`).

**What it never contains:** source code, snippets, findings, file paths, project names, messages, commit ids, user names, environment variables,
tokens or credentials. The report is checked against this list on your computer and again on the server; an unknown field is rejected.

## 4. What happens to a report
- It goes to a small Cloudflare Worker, which creates one issue in a **private** repository that only the maintainer can read. The report text is
  treated as untrusted data and is never executed. Reports may be analysed with AI development tools (the consent text says so).
- Rate limits protect the service: if you hit one, QAMAR tells you in plain words and does **not** retry automatically.
- The server keeps only a salted hash of your IP address (for rate limiting, for the current UTC day). Cloudflare, as the network provider,
  necessarily sees the connecting IP address.
- **Retention rule: 180 days.** An automated job that enforces it exists and was verified by manual runs, but its daily schedule has **not yet been
  observed running**, and it depends on the maintainer's GitHub Actions minutes. Until the schedule is confirmed, the maintainer applies the same
  rule by hand (the same program, run locally, with a dry run that is reviewed first) **at least once a month**. The intake received its first (test)
  report on 2026-11-13, so no report can be 180 days old before 2027-05-12; none of the test reports remain.
- **Deletion on request is not possible:** reports are anonymous, so a single report cannot be tied to you, and identical reports from different people
  look identical. You can keep the JSON shown by `feedback preview` as a record of what you sent. Opting out (`consent revoke`) stops future sends.

## 5. What this Beta cannot do
No free-text feedback, no replies, no way to send installation problems when the CLI does not start. Please do not post raw scan files or source code
anywhere on our behalf: they contain paths and messages from your projects.
