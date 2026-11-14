# Security advisory: QAMAR Beta v0.1.0 — scanning an untrusted Git repository

**Affected:** QAMAR Beta v0.1.0 (`qamar-0.1.0-beta-win-x64.zip`, SHA-256 `7cea0477e137db24eb256b908290f1722a6df8acb24e92c4ce928883ff2b9648`).
**Fixed in:** QAMAR Beta v0.1.1.
**Severity:** high when you scan repositories you did not create; none known for your own projects.

## What is wrong
When QAMAR scans a folder that is a Git repository, it asks Git about the repository (its revision, whether files changed). Git reads some of its behaviour from settings stored **inside the repository**. An attacker who controls a repository can set those settings so that Git starts a program of their choosing on your computer, with your permissions, as soon as you scan it. Cloning a repository does not copy those settings, so the risk is mainly folders obtained other ways (an archive that contains the hidden Git folder, a shared drive, a prepared download) and `--changed` / revision lookups on them.

A second, separate weakness: a scanned project could carry its own configuration or ignore files for the scanners and thereby hide findings, while QAMAR still reported a complete scan.

## What to do
1. Stop using v0.1.0. Download **v0.1.1** from the Releases page and verify its SHA-256 (the page and `SHA256SUMS` list it).
2. Do not scan folders you do not trust with v0.1.0. If you already did, treat the computer as potentially affected only if that folder's author wanted to attack you; we know of no real-world use.
3. After upgrading, re-scan important projects: results may now be **partial** (exit code 3) where the project hides or skips things; the report's "Scope notes" says what.

## How it was found and fixed
Found by an independent audit before any report of abuse. In v0.1.1 all Git access goes through one hardened layer and each scanner receives a neutral configuration; regression tests reproduce the problem on v0.1.0 and fail to reproduce it on v0.1.1. Details: `SECURITY_MODEL.md` in the package and in this repository's `docs` folder.

We are not publishing exploit details beyond this description.
