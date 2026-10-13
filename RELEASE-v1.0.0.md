# Giolit SVA Verifier 1.0.0 — Trial Edition

**Stop simulating. Start proving.** One of the world's first RTL assertion
verifiers that doesn't just say PASS/FAIL — it **hands you the proof itself**
(a signed certificate with the inductive invariant in your own signal names)
and **grades every bound with a safety margin** (the Giolit Safety Resilience
Score, 1–5 ★). Formal verification of SystemVerilog assertions, offline, on
your computer, in one command.

> **A proof your customer can check — or the exact clock cycle that breaks you.**
> Every `assert property` decided for ALL inputs and EVERY start state.
> No test vectors. No project files. No external solvers. No cloud.

## Download

| File | Size | SHA-256 |
|---|---|---|
| `giolit-sva-verifier-1.0.0-trial.tar.gz` | 6.7 MB | `c46a8e2085453571d9832e48805650e28e9d14f50b06bf4958915b43357e8d53` |

Verify after downloading:

```sh
shasum -a 256 -c CHECKSUMS.txt   # macOS
sha256sum -c CHECKSUMS.txt       # Linux
```

## 60 seconds to your first proof (Ubuntu 24.04)

```sh
sudo apt install libllvm18 libclang-cpp18 libclang-common-18-dev libc6-dev libssl3
tar xzf giolit-sva-verifier-1.0.0-trial.tar.gz
cd giolit-sva-verifier-1.0.0-trial/code && ./install.sh

sva-verify --reset=rst 01_counter.sv      # ALL ASSERTIONS PROVED ✦ exit 0
sva-verify --reset=rst 02_counter_bug.sv  # ASSERTION FAILURES FOUND + trace ✦ exit 1
sva-verify --reset=rst --report=cert 01_counter.sv   # ✦ cert/certificate.pdf
sva-verify --reset=rst --margins 11_margins.sv       # ✦ safety margins + ★★★☆☆
```

New here? Open [`docs/Product-Poster.pdf`](https://github.com/Giolit-Labs/Giolit-SVA-Verifier/blob/main/docs/Product-Poster.pdf)
(the product on one page), then [`docs/Tutorial.pdf`](https://github.com/Giolit-Labs/Giolit-SVA-Verifier/blob/main/docs/Tutorial.pdf)
(11 design examples, reading proofs, SDK).

## Measured, not marketed — 0 wrong answers

340 benchmark properties with a known answer · same 20 s budget per design:

| | **sva-verify 1.0** | SymbiYosys (`abc pdr`) | EBMC 6.0 |
|---|---|---|---|
| EBMC regression suite (of 168) | **163** | 55 | 114 |
| SymbiYosys examples (of 63) | **56** | 49 | 46 |
| All designs with a known answer (of 231) | **219** | 104 | 160 |
| Wrong answers | **0** | — | — |
| Signed proof certificate | ✅ only here | — | — |
| Safety margins + resilience score | ✅ only here | — | — |

Full methodology: [`docs/Tool-Comparison.pdf`](https://github.com/Giolit-Labs/Giolit-SVA-Verifier/blob/main/docs/Tool-Comparison.pdf).
Every number reproducible: **218 verifications** with console output, JSON and
`reproduce.sh` ship inside this very bundle (`benchmarks/` + `results/`).

## What's inside

- **`code/`** — `sva-verify` binary + libraries, public C++ API, **11 tutorial
  designs** (counter → FIFO → arbiter → handshake → traffic light → hierarchy
  → assumptions → liveness → lockstep → margins), **8 SDK examples**, installer
- **`documents/`** — Poster, Tutorial, README, Tool Comparison, License Terms
  (+ Markdown originals, third-party notices)
- **`benchmarks/` + `results/`** — EBMC regression suite & SymbiYosys examples
  with full evidence per design

**YOU GIVE:** SystemVerilog RTL with `assert` / `assume` / `cover` →
**THREE ENGINES RACE:** prover (invariants) · explorer (states + lassos) ·
simulator (deep random runs) →
**YOU GET:** PROVED + signed certificate · FAILED + waveform + replay testbench
· COVERED + witness · margins + ★ score · JSON for CI · C++ SDK for your tools.

## Trial licence

**1000 runs within 50 days**, one computer (`sva-verify --license` shows
status, uses no run). Licences, verification services & training:
**business.giolitlabs@gmail.com** · **www.giolit.com**

---
*Giolit Labs, Thrissur, Kerala, India — the sole owner of the Giolit SVA
Verifier. Use is subject to the License Terms.*
