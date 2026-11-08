# Last Wei

**Pitch:** Last Wei computes a deterministic proportional split of an ETH amount across four recipients, assigning every wei without losing rounding dust.

## The problem and approach

Integer division rounds each proportional share down, leaving some wei unassigned. Last Wei uses the largest-remainder method: it floors each nonzero recipient's exact share, then gives remaining wei to the largest fractional remainders. Ties go to the lower input index. Zero-weight recipients receive zero. Identical inputs always return identical outputs, and the four outputs sum exactly to the input amount.

## Fe highlight and architecture

`src/lib.fe` implements a pure allocator over a fixed-size `[u256; 4]` and exposes it through a pure Fe contract message. ETH is represented as `Wei`, while the ABI quote accepts an amount and four weights and returns four wei amounts. `src/tests.fe` tests the pure arithmetic and calls the compiled contract in Fe's test EVM.

```fe
let base = amount.mul_div(numerator: weight, denominator: total_weight)
```

The typed amount and fixed-size return make units and participant count explicit. Fe's checked arithmetic and `Wei.mul_div` are used for share calculations. The remainder product is bounded separately before multiplication.

## Build and test

The project manifest is `fe.toml`; this project was built with Fe 26.5.0. Install that compiler with the official Fe installer, then run these commands from the project directory:

```sh
curl -fsSL https://raw.githubusercontent.com/argotorg/fe/master/feup/feup.sh | bash -s -- --version v26.5.0
source "$HOME/.fe/env"
fe --version
fe fmt --check
fe build
fe test
```

No other project-specific tools are required. The build emits the contract ABI and bytecode under `out/`.

The automated tests cover conservation, payout bounds, zero weights, tie-breaking and order, larger fractional remainders, the inclusive maximum-weight boundary, zero-amount behavior, tiny amounts, large wei amounts, invalid weights, and the ABI quote path.

## Reproducible demo

Run `fe test`. The `contract_quote_matches_pure_allocator` test deploys the quote contract in Fe's test EVM and requests a 1 ETH quote for weights `[1, 2, 3, 4]`. It asserts these four outputs, in wei:

```text
100000000000000000
200000000000000000
300000000000000000
400000000000000000
```

The `largest_remainder_tie_uses_input_order` test demonstrates the tiny-amount case: 1 wei with weights `[1, 1, 1, 0]` returns `[1, 0, 0, 0]`.

## Limitations

This is a deterministic quote calculator, not a production payment splitter. It has no recipient addresses, custody, ETH transfers, access control, configurable participant count, or persistent configuration. The interface is fixed at four recipients. Each weight and their sum are limited to 1,000,000,000,000,000,000; all-zero weights and totals above that limit revert. The cap ensures the remainder calculation's intermediate product fits in `u256`. Callers remain responsible for validating weights and safely executing any eventual payments.

## Declaration

The repository preserves the Fe Builders Challenge starter's Git history and scaffold, including its layout, Apache-2.0 license, and CI setup. The official starter is https://github.com/fe-lang/builders-challenge-starter. The splitter logic, tests, and project README were written after the build window opened on November 3, 2026 (UTC); no external splitter implementation was copied. AI assistance was used to implement, document, and test this project. The project changes are committed locally on top of the starter history; they have not been pushed, published, registered, deployed, or submitted.

## Team

Solo project by `solreaper fe/wei- splitter` (the supplied Git commit identity). Contact: `solreaper93@gmail.com`.

## License

Apache License 2.0, in `LICENSE`.
