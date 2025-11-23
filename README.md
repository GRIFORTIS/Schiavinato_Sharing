## Schiavinato Sharing: Human-Executable Secret Sharing for BIP39 Mnemonics
A Pencil-and-Paper Arithmetic Scheme for Inheritance and Disaster Recovery

This repository hosts the whitepaper:

> **“Schiavinato Sharing: Human-Executable Secret Sharing for BIP39 Mnemonics”**

**[`GRIFORTIS/Schiavinato_Sharing/WHITEPAPER.md`](https://github.com/GRIFORTIS/Schiavinato_Sharing/blob/main/WHITEPAPER.md)**

The goal of this project is to document and provide reference material for a secret-sharing scheme tailored to BIP39 mnemonics using **basic arithmetic**, designed to be performed entirely **by hand**. The primary artifacts currently available are the whitepaper and test vectors; reference implementations will be developed under the `tools/` subdirectory.

---

## Contents

- `WHITEPAPER.md` – full technical description of the Schiavinato BIP39 mnemonic sharing scheme.
- `TEST_VECTORS.md` – reproducible $GF(2053)$ test vector for a 2-of-3, 12-word example.
- `CONTRIBUTING.md` – guidelines for suggesting changes, reference implementations, and reviews.
- `SECURITY.md` – how to report vulnerabilities or subtle failure modes responsibly.
- `LICENSE` and `LICENSE-WHITEPAPER.md` – licensing terms for code and the whitepaper, respectively.
- `tools/html/` – self-contained HTML/JavaScript reference tool for sharding and recovery (available now).
- `tools/python/` – planned Python reference library implementing the Schiavinato Sharing scheme.

The HTML tool is currently available for testing and review. Additional resources (including the Python library and further examples) will be added to this repository over time.

---

## How to use this repository

- **Read the scheme**: start with `WHITEPAPER.md` for the full mathematical and conceptual description.
- **Discuss or ask questions**: open a GitHub issue in this repository if you have questions, suggestions, or find mistakes in the document.
- **Validate implementations**: use `TEST_VECTORS.md` to confirm that your sharing and recovery code matches the reference arithmetic in $GF(2053)$.

If you build an implementation based on this scheme, consider linking back to this repository in your project’s documentation.

---

## Contributing

Contributions are welcome, especially in the form of:

- **Feedback on the whitepaper** (clarity, correctness, examples).
- **Errata reports** for typos, ambiguities, or mistakes.
- **Reference implementations** and example code in any programming language.

To propose changes:

1. Fork the repository.
2. Create a feature branch.
3. Commit your changes with clear messages.
4. Open a pull request describing what you changed and why.

---

## License

This repository uses a **dual license model**:

- **Code and reference implementations** (now or added in the future) are licensed under the **MIT License**.  
  - See `LICENSE` for the full text.
- The whitepaper `WHITEPAPER.md` is licensed under the **Creative Commons Attribution 4.0 International License (CC BY 4.0)**.  
  - See `LICENSE-WHITEPAPER.md` for details.

In practical terms:

- You may use, modify, and integrate any code in this repo—if present—into open-source or commercial projects under the MIT terms, with appropriate attribution.
- You may share and adapt the whitepaper, including for commercial use, as long as you credit the authors and indicate if changes were made, in line with CC BY 4.0.



