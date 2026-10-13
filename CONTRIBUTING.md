# Contributing to Giolit-SVA-Verifier

Thanks for your interest in the Giolit SystemVerilog Assertion Verifier.

## What this repository is

This repository hosts Giolit Labs' **trial bundle**
(`giolit-sva-verifier-1.0.0-trial.tar.gz`, verified by `CHECKSUMS.txt`).
Inside the bundle: the `sva-verify` verifier binary and shared libraries
(`code/bin`, `code/lib`), the public C++ API headers (`code/include/giolit`,
`code/include/vtask`), tutorial examples, the SDK examples, documents,
benchmarks and their result evidence. The verification engines themselves
are distributed as binaries; only the documented entry points in
`code/include` are public.

## What we accept

- Bug reports with a minimal SystemVerilog reproducer, the exact
  `sva-verify` command, `sva-verify --version` / `sva-verify --license`
  output, and the result bundle (`--report=DIR`) if shareable.
- Documentation fixes and tutorial feedback.
- New or improved benchmarks (RTL designs with a known PROVED/FAILED answer).
- SDK example improvements (C++17, must build with `build.sh` inside the
  installed `share/giolit/sdk-examples`).

We do **not** accept reverse-engineering of the binaries, licence-circumvention,
or removal of trial markings (see `LICENSE.md`, sections 2, 4.4 and 5).

## Reporting issues

1. Check `docs/Tutorial.pdf` (troubleshooting + “what to do with UNDECIDED”,
   §11) and the table in `README.md`.
2. Verify integrity: `sha256sum -c CHECKSUMS.txt`, then after extracting,
   `sha256sum -c SHA256SUMS` inside the bundle folder.
3. Open an issue with the template: OS (`Ubuntu 24.04`?), exact command,
   expected vs actual result, reproducer files, `verifier-output.txt`.

## Benchmarks

- Add the `.sv`/`.v` file(s) under the right `benchmarks/<suite>/` folder.
- State the expected verdict (which assertions PROVED / which FAILED) and
  the `sva-verify` options (`--top`, `--reset`, `-D`, …) in your PR
  description; maintainers will regenerate the `results/` evidence.

## SDK examples

- Follow the four-step pattern (`acquireToken` → `verifySva` → read
  `SvaResult::properties` → optional `addMargins` / `writeReport`).
- Every library entry point needs a valid token from `acquireToken()`; do
  not cache tokens across runs.
- Test on Ubuntu 24.04 with `libllvm18 libclang-cpp18
  libclang-common-18-dev libc6-dev libssl3` installed.

## Pull requests

- Keep PRs focused; one benchmark family or one example per PR.
- Do not commit report output directories (`out/`, `cert/`, `--report`
  targets), locally generated PDFs/VCDs, or `~/.giolit` state.
  The `*.tar.gz` bundle itself is versioned — only maintainers update it.
- CI (`ci.yml`) must pass: install check, `01_counter.sv` → exit 0,
  `02_counter_bug.sv` → exit 1, SDK builds.

## Contact

Security issues: see `SECURITY.md`. Commercial questions (licences,
verification services, training): `business.giolitlabs@gmail.com`.
