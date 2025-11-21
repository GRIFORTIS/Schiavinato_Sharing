## Schiavinato Sharing Python Library (Planned)

This directory will host the reference **Python implementation** of the Schiavinato Sharing scheme, as outlined in Section 5.2 of `WHITEPAPER.md`.

Planned components include:

- A `schiavinato_sharing` package exposing high-level functions such as:
  - `split_bip39(mnemonic, k, n, rng=None) -> List[MnemonicShare]`
  - `recover_bip39(shares: Sequence[MnemonicShare]) -> str`
- Support code for arithmetic in \(GF(2053)\), Lagrange coefficient computation, and BIP39 word/index conversions.
- Automated tests that verify behavior against the canonical `TEST_VECTORS.md`.

The library will be released under the **MIT License** to facilitate integration into other systems and independent security review.


