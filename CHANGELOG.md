# Changelog

All notable changes to the Giolit SVA Verifier distribution in this repository.

## [1.0.0] — Trial edition (2026)

Initial public repository release (`giolit-sva-verifier-1.0.0-trial`).

- Verifier command `sva-verify` (Linux x86-64, Ubuntu 24.04, LLVM/Clang 18)
  for SystemVerilog assertions (IEEE 1800-2017): every `assert property`,
  `cover property` and immediate assertion decided for all inputs and every
  start state — PROVED / FAILED / COVERED / UNREACHABLE / REACHED /
  UNDECIDED / UNSUPPORTED; `assume property` constrains inputs; liveness
  (`s_eventually`, strong sequences, `s_until`) proved by k-liveness,
  refuted by a lasso.
- Three in-house engines raced automatically: prover (inductive invariants),
  explorer (exhaustive states, lassos), simulator (deep random runs); no
  external solver, fully offline.
- Evidence bundles (`--report=DIR`): PDF certificate with the proofs (or
  counterexample / verification report), `proof.json`, VCD waveforms,
  replay testbenches, signed statement (`certificate.json`), `reproduce.sh`.
- Safety margins (`--margins`, in every `--report`) + Giolit Safety
  Resilience Score (1–5 stars) for every proved numeric bound.
- Public C++ SDK (`code/include/giolit`, `code/include/vtask`) + 8 example
  clients in `share/giolit/sdk-examples`: `verify_design`,
  `counterexamples`, `proofs`, `margins`, `certify`, `regression`,
  `low_level`, `license_info`.
- 11 tutorial examples (`share/giolit/examples`: counter, FIFO, arbiter,
  handshake, traffic light, hierarchy, assumptions, liveness, lockstep,
  margins); Tutorial, Product Poster, Tool Comparison, README and Licence
  Terms in `documents/`.
- 218 benchmark verifications with known answers (EBMC regression suite +
  SymbiYosys examples) in `benchmarks/` + full evidence in `results/`
  (`SUMMARY.csv`); 0 wrong answers on 340 benchmark properties, 272 of 277
  EBMC properties decided (see `documents/Tool-Comparison.md`).
- Trial licence: 1000 runs within 50 days; build validity 100 days.
  Contact `business.giolitlabs@gmail.com` / `www.giolit.com` for paid licences.

[1.0.0]: https://github.com/Giolit-Labs/Giolit-SVA-Verifier/releases/tag/v1.0.0
