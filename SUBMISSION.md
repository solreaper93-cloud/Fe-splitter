# Last Wei submission reference

**Pitch:** Last Wei computes a deterministic proportional split of an ETH amount across four recipients, assigning every wei without losing rounding dust.

## Implementation

A pure Fe allocator uses the largest-remainder method, with ties resolved by recipient input order. The quote contract calculates four wei outputs and does not hold or transfer ETH. The interface is fixed at four recipients.

## Reproducible check

Built and tested with Fe 26.5.0. From the project directory:

```sh
fe --version
fe fmt --check
fe build
fe test
```

Local verification on November 5, 2026: formatting check passed, build passed, and all 11 tests passed. The reproducible demo is the `contract_quote_matches_pure_allocator` test in `src/tests.fe`.

## License and source

Apache License 2.0, see `LICENSE`. The project retains the Fe Builders Challenge starter Git history and CI scaffold; the project implementation and tests are in `src/`.

## Repository

Target repository: https://github.com/solreaper93-cloud/Fe-splitter

The repository contains the project source and preserves the Fe Builders Challenge starter history. No deployment or contest registration/submission is claimed here.
