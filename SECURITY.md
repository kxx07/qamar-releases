# Security policy

QAMAR is a Beta and, by design for now, has **no support, bug-report or vulnerability-intake channel**: the only feedback path is the optional, automated, anonymous report described in
[docs/FEEDBACK.md](docs/FEEDBACK.md). This repository has Issues, Wiki and Actions disabled.

If you find a vulnerability in QAMAR itself (for example a way a crafted project could make the scanner run code, write outside its folders, or send data it should not), please do
not publish exploit details; the maintainer may add a private reporting channel in a later release. Vulnerabilities in the third-party scanners QAMAR downloads (OpenGrep,
Gitleaks, OSV-Scanner, zizmor, Biome, Trivy, Syft, actionlint, ast-grep) belong to those projects.
