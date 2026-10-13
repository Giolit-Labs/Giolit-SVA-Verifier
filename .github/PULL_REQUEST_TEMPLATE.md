# Pull request

## What does this change?

<!-- Benchmark, docs, SDK example, CI… Link the issue if any. -->

## Verification

- [ ] `sha256sum -c CHECKSUMS.txt` passes (if the bundle changed)
- [ ] New/changed benchmarks state the expected verdict (which assertions PROVED / FAILED) + `sva-verify` options
- [ ] SDK examples still build with `build.sh` in the installed `share/giolit/sdk-examples` (if touched)
- [ ] No locally generated reports, VCDs, PDFs or licence state committed

## Notes for reviewers

<!-- Reproducer command, result bundle location, anything unusual. -->
