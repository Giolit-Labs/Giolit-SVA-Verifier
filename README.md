<p align="center">
  <img src="docs/logo.png" alt="Giolit Labs logo" width="220">
</p>

# Giolit SVA Verifier 1.0 — Trial Edition

[![CI](https://github.com/Giolit-Labs/Giolit-SVA-Verifier/actions/workflows/ci.yml/badge.svg)](https://github.com/Giolit-Labs/Giolit-SVA-Verifier/actions/workflows/ci.yml)
[![Platform](https://img.shields.io/badge/platform-Ubuntu%2024.04%20x86__64-blue)](https://github.com/Giolit-Labs/Giolit-SVA-Verifier/releases)
[![Version](https://img.shields.io/badge/v1-1.0.0-green)](CHANGELOG.md)
[![License](https://img.shields.io/badge/license-Trial_1000_runs_50_days-orange)](LICENSE.md)

**Stop simulating. Start proving. — Formal proof that every SystemVerilog assertion in your RTL holds for ALL inputs and EVERY start state, or the exact clock cycle that breaks it.**

No test vectors. No project files. No external solvers. No cloud — it runs offline, on your computer. One command turns your RTL into either a **signed proof certificate your customer can check**, or a **cycle-by-cycle trace, waveform and replay testbench** that reproduces the bug in your own simulator.

> One of the world's first RTL assertion verifiers to do all three in a single run: **prove every SVA property** (safety, covers *and* liveness), **hand you the proof itself** (the inductive invariant, in your own signal names), and **grade every bound with a safety margin** — the Giolit Safety Resilience Score, 1 to 5 stars. Measured, not marketed: **0 wrong answers on 340 benchmark properties** (see [Tool Comparison](#measured-not-marketed-0-wrong-answers)).

| Result | Meaning |
|---|---|
| **PROVED** | No run of the design violates the assertion. A proof, not a test result. |
| **FAILED** | A real run violates it — printed clock cycle by clock cycle, with VCD + replay testbench. |
| **COVERED** / **UNREACHABLE** | A cover property: a witness run exists / no run can ever reach it. |
| **REACHED** | A liveness goal is reached on some run (witness shown), but not proved for every run. |
| **UNDECIDED** | Not decided within the time budget — the reason is printed. |
| **UNSUPPORTED** | The property uses a construct the verifier does not model — the reason is printed. |

Checks: every `assert property`, `cover property` and immediate assertion in IEEE 1800-2017 SystemVerilog — sequences (`##[m:n]`, `[*]`, `[->]`, `[=]`, `|->`, `|=>`, `until` …), liveness (`s_eventually`, proved by k-liveness, refuted by a lasso), `assume property` constraints, `$past`/`$stable`/`$rose`/`$fell`, hierarchy, parameters, `generate`, interfaces, packages, `bind`, single clock or exact `--multiclock` semantics.

Under the hood, **three engines race on the same model — a prover** computing inductive invariants, **an explorer** enumerating reachable states and lassos, **a simulator** running deep random inputs — all built in-house, all automatic.

> Trial edition: **1000 runs within 50 days** on one computer. Trial documents are watermarked. A purchased licence produces professional, password-protected documents with your licence number. See [Licensing](#licensing).

---

## Download

This repository hosts the trial bundle — both in the tree and as a Release asset:

- **`giolit-sva-verifier-1.0.0-trial.tar.gz`** (6.7 MB) — the complete package: verifier, SDK, documents, benchmarks and result evidence.
- **`CHECKSUMS.txt`** — SHA-256 of the bundle. Verified:

```text
c46a8e2085453571d9832e48805650e28e9d14f50b06bf4958915b43357e8d53  giolit-sva-verifier-1.0.0-trial.tar.gz
```

Verify after cloning:

```sh
shasum -a 256 -c CHECKSUMS.txt   # macOS
sha256sum -c CHECKSUMS.txt       # Linux
```

The same two files are attached to the [`v1.0.0` Release](https://github.com/Giolit-Labs/Giolit-SVA-Verifier/releases/tag/v1.0.0) for download without cloning.

## Documents (preview without downloading the bundle)

`docs/` mirrors `documents/` inside the bundle, so GitHub renders previews directly:

- [Product-Poster.pdf](docs/Product-Poster.pdf) — the product on one page (start here for the hype, stay for the numbers)
- [Brochure.pdf](docs/Brochure.pdf) — the product brochure
- [Tutorial.pdf](docs/Tutorial.pdf) / [TUTORIAL.md](docs/TUTORIAL.md) — proving SVA properties step by step (11 design examples, reading proofs, margins, SDK)
- [README.md](docs/README.md) / [README.pdf](docs/README.pdf) — installation, first steps, commands
- [Tool-Comparison.pdf](docs/Tool-Comparison.pdf) / [Tool-Comparison.md](docs/Tool-Comparison.md) — measured against SymbiYosys and EBMC on public suites
- [License-Terms.md](docs/License-Terms.md) / [License-Terms.pdf](docs/License-Terms.pdf) (authoritative terms are also at [`LICENSE.md`](LICENSE.md))
- [THIRD-PARTY-NOTICES.md](docs/THIRD-PARTY-NOTICES.md)

## Quick start (Ubuntu 24.04 x86-64)

```sh
sudo apt update
sudo apt install libllvm18 libclang-cpp18 libclang-common-18-dev libc6-dev libssl3
# PDFs (recommended)
sudo apt install texlive-luatex texlive-latex-recommended texlive-latex-extra \
                 texlive-pictures texlive-fonts-extra qpdf

tar xzf giolit-sva-verifier-1.0.0-trial.tar.gz
cd giolit-sva-verifier-1.0.0-trial/code
./install.sh   # → ~/.local/giolit-sva-verify, command in ~/.local/bin

sva-verify --version   # sva-verify 1.0.0 (trial version)
sva-verify --license   # runs and days left (uses no run)

cp -r ~/.local/giolit-sva-verify/share/giolit/examples ~/sva-tutorial
cd ~/sva-tutorial
sva-verify --reset=rst 01_counter.sv                  # ALL ASSERTIONS PROVED (exit 0)
sva-verify --reset=rst 02_counter_bug.sv              # ASSERTION FAILURES FOUND + trace (exit 1)
sva-verify --reset=rst --report=cert 01_counter.sv     # cert/certificate.pdf + VCD + replay testbench
sva-verify --reset=rst --margins 11_margins.sv         # safety margins + ★ resilience score
```

Without LuaLaTeX the verifier works fully (console + JSON); only the PDF is skipped.

## What's inside the bundle

| Path (inside the `.tar.gz`) | Contents |
|---|---|
| `code/` | `bin/sva-verify`, `lib/libgiolit_*.so`, `include/giolit` + `include/vtask` (public C++ API), `share/giolit/examples/` (11 tutorial designs: counter, FIFO, arbiter, handshake, traffic light, hierarchy, assumptions, liveness, lockstep, margins), `share/giolit/sdk-examples/` (8 API clients), `install.sh` / `uninstall.sh` |
| `documents/` | `Product-Poster.pdf` (one page), `Brochure.pdf`, `Tutorial.pdf` + `TUTORIAL.md` (start here), `README.md` / `README.pdf`, `Tool-Comparison.md` / `Tool-Comparison.pdf`, `License-Terms.md` / `License-Terms.pdf`, `THIRD-PARTY-NOTICES.md` — mirrored at [`docs/`](docs/) for in-browser preview |
| `benchmarks/` | Public benchmark designs with a known answer: the **EBMC regression suite** + **SymbiYosys examples**, with their licences. `MANIFEST.tsv` lists all **218 verifications** (expected result, options, files). |
| `results/` | Result evidence for every benchmark in `MANIFEST.tsv`: console output (`verifier-output.txt`), `results.json`, `reproduce.sh`. `SUMMARY.csv` lists them all. |
| `SHA256SUMS` | Checksums of every file *inside* the bundle (`sha256sum -c SHA256SUMS` after extracting) |
| `README.txt` | Short package readme |

## Measured, not marketed — 0 wrong answers

From [`docs/Tool-Comparison.pdf`](docs/Tool-Comparison.pdf) (v1.1, 08 Oct 2026, same 20 s budget per design for every tool):

| | **sva-verify 1.0** | SymbiYosys (open-source flow, `abc pdr`) | EBMC 6.0 (engine portfolio) |
|---|---|---|---|
| **EBMC regression suite**: designs decided correctly (of 168 with a known answer) | **163** | 55 | 114 |
| **SymbiYosys examples**: designs decided correctly (of 63 with a known answer) | **56** | 49 | 46 |
| **All 231 designs with a known answer** | **219** | 104 | 160 |
| Wrong answers on 340 benchmark properties | **0** | — | — |
| Proof certificate (signed inductive invariant) | ✅ | — | — |
| Safety margin + resilience score (1–5 ★) | ✅ | — | — |

Honest footnote (it's in the paper too): on the 12 TACAS 2016 bit-level netlists with no published answers, SymbiYosys' mature `abc pdr` and EBMC's SAT engines decide more designs (7 and 6 vs our 4) within the budget — bit-level IC3 is their home turf. Everywhere SVA language coverage matters, Giolit wins by a landslide, because it reads full IEEE 1800-2017 through the slang elaborator instead of a Verilog subset.

## Certificates, margins and the resilience score

```sh
sva-verify --reset=rst --report=cert --issued-to="Acme Silicon" 01_counter.sv
# Report: cert (VERIFIED, document GIO-SVA-...): cert/certificate.pdf
```

- **Certificate** (`--report=DIR`): when everything is proved, a PDF certificate with the proofs — each an *inductive invariant* over your signals (`b = a ∧ 0 <= b <= 12000000`), checkable by hand (Tutorial §16). Failures come as timing diagrams + VCD waveforms + replay testbenches that fail in your own simulator. Everything is bound by SHA-256 in a signed statement your customer verifies with one script.
- **Safety margins** (`--margins`, included in `--report`): every proved numeric bound is tightened until it fails — the distance between what your design does and what it may never do. The weakest bound sets the **Giolit Safety Resilience Score** ★–★★★★★ on the certificate cover. Margin 0 isn't an error — it means any change toward the bound flips the verdict. Now you know.

## Commands

```sh
sva-verify [options] design.sv [more.sv ...]   # files are elaborated and linked together
```

| Option | Meaning | Default |
|---|---|---|
| `--top=MODULE` | Top-level module (else the only uninstantiated one) | auto |
| `--reset=SIGNAL` | Reset active in the first cycle (`rst`, or `'!rst_n'` for active low) | — |
| `--property=NAME` | Verify only this property, on its cone of influence | all |
| `-p EXPR` | Also assert `always EXPR` on the top module | — |
| `-G NAME=VALUE` | Override a parameter of the top module (data-width reduction!) | — |
| `-I DIR`, `-D NAME[=V]`, `--sv-args ...` | Preprocessor / front-end options, as for your simulator | — |
| `--multiclock` | Exact edge semantics for several or derived clocks | one global clock |
| `--encoding=MODE` | `hybrid`, `word`, `bit`, or `race` (all three in parallel) | `hybrid` |
| `--timeout=SECONDS` | Total time budget | `60` |
| `--json=FILE` | Per-property results with traces as JSON (perfect for CI) | — |
| `--report=DIR` | Evidence bundle: PDF certificate/report, proofs, VCDs, testbenches, signed statement | — |
| `--issued-to=NAME` | Recipient named on the certificate (with `--report`) | — |
| `--margins` | Safety margins + resilience score (included in `--report`) | off |
| `--verbose` | Property source text and full traces | off |
| `--license`, `--version`, `--help` | Licence status (uses no run), version, usage | — |
| `--activate=FILE` | Apply a purchased licence | — |

Exit codes: `0` proved · `1` assertion fails · `2` undecided/unsupported · `3` usage/input error · `4` no valid licence.

Stuck on UNDECIDED? Tutorial §11 in order: `--reset`, more `--timeout`, `--property`, `-G` width reduction, real `assume`s, helper invariants, `--encoding=race`.

## SDK and CI

The bundle's `share/giolit/sdk-examples/` has **eight** C++ API clients (`verify_design`, `counterexamples`, `proofs`, `margins`, `certify`, `regression`, `low_level`, `license_info`; build with `./build.sh`). Four steps: `acquireToken()` → `verifySva()` → read `SvaResult::properties` → optional `addMargins` / `writeReport`. Gate builds on the exit code and `--json` output. See `.github/workflows/ci.yml` for a working Ubuntu 24.04 example.

## Licensing

Trial: **1000 runs within 50 days** of the first run; the build stops accepting trial runs 100 days after it was made. `sva-verify --license` shows status (uses no run). State lives in `~/.giolit/sva-verify` and is tamper-protected.

Purchase (terms, volumes, verification services, training): **business.giolitlabs@gmail.com** · **www.giolit.com**. Full terms: [`LICENSE.md`](LICENSE.md) (also [`docs/License-Terms.pdf`](docs/License-Terms.pdf)).

## Support

Giolit Labs, Ramavarmapuram P.O., Thrissur, Kerala, India — **www.giolit.com** — **business.giolitlabs@gmail.com**. Include `sva-verify --version`, `sva-verify --license`, and the result bundle if shareable.
