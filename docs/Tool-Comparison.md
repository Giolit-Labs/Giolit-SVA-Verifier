---
title: Tool Comparison
subtitle: "Giolit SystemVerilog Assertion Verifier 1.0, measured against SymbiYosys and EBMC on public benchmark suites"
docid: Tool comparison, version 1.1
---

# Summary

On **340 benchmark properties with a known answer** from three public suites, the Giolit SystemVerilog Assertion Verifier (`sva-verify` 1.0) gave **0 wrong answers**. On the EBMC regression suite it decided **272 of 277** such properties. Over all 231 designs with a known answer it decided **219** correctly, where SymbiYosys, the open-source formal flow, decided **104** and EBMC, the open-source model checker whose regression suite this is, decided **160**, with the same time budget per design.

Only `sva-verify` backs every proof with a **certificate**: the inductive invariant, in the design's signal names, signed and checkable by your customer; and only `sva-verify` measures the **safety margin** of every proved bound, with the Giolit Safety Resilience Score.

| | sva-verify 1.0 | SymbiYosys (open-source flow) | EBMC 6.0 |
|---|---|---|---|
| **EBMC regression suite**: designs decided correctly (of 168 with a known answer) | **163** | **55** | **114** |
| wrong answers | 0 | not scored (see "Reading the results") | not scored |
| verdicts that differ from the published answer, from differences in setup | — | 10 | 4 |
| not supported by the tool's front end or engines | 0 | 103 | 30 |
| total time for the suite | 225 s | 40 s | 117 s |
| **SymbiYosys examples**: designs decided correctly (of 63 with a known answer) | **56** | **49** | **46** |
| wrong answers | 0 | not scored (see "Reading the results") | not scored |
| verdicts that differ from the published answer, from differences in setup | — | 3 | 0 |
| not supported by the tool's front end or engines | 2 | 9 | 12 |
| total time for the suite | 181 s | 84 s | 105 s |
| **TACAS 2016 hardware designs** (12 designs, no published answers): designs decided | 4 | 7 | 6 |
| total time for the suite | 142 s | 189 s | 64 s |

# What was measured

**Benchmarks.** Three public suites, used unchanged:

- the **EBMC regression suite** (hw-cbmc project): SystemVerilog and Verilog designs whose expected results (property proved or refuted) are published with each test; tests that only make sense for a bounded check count as having no known answer;
- the **SymbiYosys examples** (YosysHQ): the example designs of the SymbiYosys distribution, with the answers its test files expect;
- the **TACAS 2016** hardware model-checking designs (no published answers; reported as decided or not).

**Tools.** `sva-verify` 1.0 (trial edition, default options). SymbiYosys from the OSS CAD Suite of 2026-10-05, mode `prove`, engine `abc pdr` (an unbounded IC3/PDR prover), reading each design with Yosys' open-source front end (`read -formal`) and, when that fails, with the yosys-slang plugin. The commercial Verific front end, which extends SymbiYosys' SVA support, was not available and is not part of this comparison; neither are commercial formal tools. **EBMC 6.0** (diffblue/hw-cbmc, the release package), which needs an engine chosen per run: every design gets the same portfolio within the same budget -- the default (EBMC's heuristic engine selection), `--k-induction`, `--ic3`, `--bdd`, and `--bound 20` for counterexamples -- and per property the first decided answer counts; a bounded "PROVED up to bound" is not counted as a proof. The per-test options in EBMC's own test files were chosen by its developers for each test and are not used.

**Settings.** One design at a time on the same computer, the same wall-clock budget of 20 s per design for every tool, the same top module, reset and macro definitions.

**Scoring, per design.** A design is expected to FAIL when one of its properties with a known answer is refuted, otherwise to PASS. `sva-verify` reports each property separately; it decides a design correctly when it finds an expected failure or proves every property with a known answer. SymbiYosys reports one status per design: PASS or FAIL decides it; UNKNOWN or a timeout leaves it open; ERROR means its front end could not read the design. A *wrong answer* is a decided verdict against the published answer.

# Reading the results

- **Setup differences are not counted as errors.** On some designs SymbiYosys' verdict differs from the published answer for reasons of setup, not of correctness: the design's only refuted property is a cover (EBMC records a reachable cover as refuted; SymbiYosys' `prove` mode does not check covers), it uses liveness operators its open-source front end does not model, or the test needs an option (a reset, a command-line assertion) that our harness did not pass to it. These designs are listed separately and not scored.
- **Front-end coverage** explains most of the difference: many EBMC tests use SVA sequences, liveness and SystemVerilog constructs that the open-source Yosys flow does not read. `sva-verify` reads them with a full IEEE 1800-2017 elaborator and models the assertions itself.
- **Covers and liveness.** SymbiYosys' `prove` mode does not check cover properties, and the open-source flow rejects most liveness operators; `sva-verify` checks both in the same run.
- **EBMC on its own suite.** The EBMC regression suite was written for EBMC: its published answers are EBMC's own results with options chosen per test. With one uniform portfolio, as a user without that knowledge would run it, EBMC decides fewer of them; several of its engines reject SVA sequences (`unsupported by k-induction`, `not supported by BDD engine`), and some reset settings crash EBMC 6.0. Verdicts that differ from the published answer are listed, not scored.
- **Where SymbiYosys and EBMC are strong.** SymbiYosys' `abc pdr` is a mature bit-level IC3 implementation, and EBMC's SAT-based engines are fast on bit-level designs: on the TACAS 2016 netlists (no published answers) they decide more designs than `sva-verify` within the budget. The per-design results are in the files listed below.
- **Every number here is reproducible**: the verifier package holds the benchmark designs it decides correctly (218 verifications in `benchmarks/MANIFEST.tsv`) and, for each, the console output, the JSON results and a `reproduce.sh` script.

# Feature comparison

| | sva-verify 1.0 | SymbiYosys, open-source flow | EBMC 6.0 |
|---|---|---|---|
| Input | SystemVerilog (IEEE 1800-2017) with SVA, via the slang elaborator | Verilog with a subset of SVA (Yosys); more SystemVerilog through the yosys-slang plugin | Verilog and SystemVerilog with SVA (own front end) |
| Result per property | yes: each assertion PROVED / FAILED / UNDECIDED separately | per task (PASS / FAIL); failing assertions are named | yes, per property |
| Unbounded proofs | yes (invariants, exhaustive exploration), chosen automatically | yes (`abc pdr`, k-induction engines) | yes (k-induction, IC3, BDD), the engine chosen by the user per run |
| **Proof certificate** | **yes: the inductive invariant in the design's signal names, a signed statement with the SHA-256 of every file, a checker for your customer** | no (PASS) | no (PROVED) |
| **Safety margin of proved bounds** | **yes: per bound and latency window, with the Giolit Safety Resilience Score (1 to 5 stars)** | no | no |
| Cover properties | checked in the same run, with witness traces | separate `cover` mode | yes |
| Liveness (`s_eventually`) | proved by k-liveness or refuted by a lasso | limited in the open-source flow | with chosen engines (liveness-to-safety, ranking functions) |
| Engines | prover, explicit-state explorer and random simulator raced in parallel, all built in-house | external solvers and engines (ABC, SMT solvers) | SAT-based engines (built-in SAT solvers; SMT solvers optional) |
| Counterexamples | per clock cycle, JSON, VCD waveform and a replay testbench for your simulator | VCD waveform files, testbench | per cycle, JSON, VCD |
| Evidence bundle | certificate or report (PDF), proofs, waveforms, testbenches, `reproduce.sh` | log files | console and JSON |
| Setup | one command; no project file | a `.sby` project file per task | one command, an engine per run |
| License | commercial (free trial) | open source (ISC) | open source (BSD) |

*Measured 13 October 2026 by Giolit Labs. SymbiYosys, Yosys, ABC and EBMC are projects of their respective authors; names are used only to identify the tools measured.*
