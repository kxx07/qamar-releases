# QAMAR releases

Public downloads and documentation for **QAMAR**, a local security and code-quality scanner for software projects (Windows 11 x64 Beta).

- **Download:** the latest file on the [Releases page](../../releases): `qamar-0.1.0-beta-win-x64.zip`. SHA-256 of Beta 0.1.0-beta: `7cea0477e137db24eb256b908290f1722a6df8acb24e92c4ce928883ff2b9648`
- **Install and first scan:** [docs/INSTALL.md](docs/INSTALL.md)
- **Known limitations:** [docs/KNOWN_LIMITATIONS.md](docs/KNOWN_LIMITATIONS.md) (read this before relying on any result)
- **Feedback and privacy:** [docs/FEEDBACK.md](docs/FEEDBACK.md) (optional, anonymous, automated only; there is no support channel in this Beta)
- **Third-party licences:** [docs/THIRD_PARTY_NOTICES.md](docs/THIRD_PARTY_NOTICES.md)
- **Release notes:** [releases/v0.1.0-beta/RELEASE_NOTES.md](releases/v0.1.0-beta/RELEASE_NOTES.md)
- **Security policy:** [SECURITY.md](SECURITY.md)

This repository holds documentation and release notes only; the ZIP is attached to the release. QAMAR's development repository is private. The ZIP contains the command-line
tool's JavaScript source and rules under the Apache-2.0 licence (see [LICENSE](LICENSE)); the scanner programs it downloads on first run are separate projects under their own licences.

**Not a security guarantee.** No scanner proves that code is secure. The ZIP and the scanner programs are not code-signed: always verify the SHA-256.
