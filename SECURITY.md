# Security Policy

## Supported versions

| Version | Supported |
|---|---|
| 1.0.x (trial) | ✅ Supported — please update to the newest `v1.0.0-trial` build from Releases |

The verifier runs entirely on your computer and needs no network connection.
It does not transmit designs or results to Giolit Labs.

## Reporting a vulnerability

**Do not open a public issue for security vulnerabilities.**

Email **business.giolitlabs@gmail.com** with:

- `sva-verify --version` and `sva-verify --license` output,
- OS and installed library versions (`libllvm18`, `libclang-cpp18`, `libssl3`),
- the reproducer (SystemVerilog files + exact command) or a description of the finding,
- whether result bundles / PDFs may be shared.

We will acknowledge receipt within 3 business days and keep you informed until
a fix or mitigation is released.

## Scope notes

- The licence state in `~/.giolit/sva-verify` (`GIOLIT_HOME`) is tamper-protected.
  Do not attempt to reset, alter or transfer it — this breaches `LICENSE.md`
  §4.4 and invalidates support.
- Result bundles record the analysed files' names and hashes. Review them
  before sharing — strip paths if needed.
- Licensed certificates are password-protected PDFs (needs `qpdf` or
  `mupdf-tools`); keep `certificate-password.txt` confidential alongside the PDF.
