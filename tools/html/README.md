## Schiavinato Sharing HTML Tool

This directory contains the reference **self-contained HTML/JavaScript tool** described in Section 5.1 of `WHITEPAPER.md`:

- `schiavinato_sharing.html` – A single-page application designed to run fully offline on an air-gapped machine.  
- Features include guided sharding and recovery workflows, a manual sharding helper, and a Lagrange coefficient calculator.
- `Manual Test Checklist/` – Comprehensive manual test documentation for quality assurance:
  - `RECOVERY_TESTS.md` – Test scenarios for the wallet recovery flow
  - `CREATE_TESTS.md` – Test scenarios for the share creation flow

The HTML tool is a complete implementation released under the MIT License. While functional, it remains **experimental and unaudited** – see the whitepaper's status warnings before using it to secure real funds.

### Manual Test Checklist

The `Manual Test Checklist/` folder contains structured test scenarios for exploratory testing of the HTML tool's main workflows. These tests are designed to be performed manually on an offline, air-gapped machine before each release or after significant UI changes.

**Purpose:**
- Ensure consistent quality across releases
- Document expected behavior for each user flow
- Catch regressions and edge cases before deployment
- Provide a baseline for future automated testing

**Test Files:**
- **`RECOVERY_TESTS.md`** covers the wallet recovery workflow, including validation of share inputs, checksum verification, master verification, BIP39 checksum validation, and error handling scenarios.
- **`CREATE_TESTS.md`** covers the share creation workflow, including BIP39 mnemonic validation, scheme selection, metadata handling, share generation verification, and navigation flows.

Each test file follows the same structure: numbered scenarios with step-by-step instructions and clear expected outcomes. Testers should work entirely offline using the HTML file opened directly from disk, and document any deviations or bugs in the project's issue tracker.
