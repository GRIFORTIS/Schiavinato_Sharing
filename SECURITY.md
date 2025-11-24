## Security Policy for Schiavinato Sharing

Schiavinato Sharing follows the **organization-wide GRIFORTIS security policy** for reporting vulnerabilities and handling sensitive findings:

- See the canonical policy in the `.github` repository:  
  **[`GRIFORTIS/.github/SECURITY.md`](https://github.com/GRIFORTIS/.github/blob/main/SECURITY.md)**

The sections below provide Schiavinato Sharing–specific context and should be read together with the organization-wide policy.

Schiavinato Sharing is intended to help users protect real assets. We take potential security issues seriously and ask that you follow this policy when reporting vulnerabilities or subtle failure modes.

---

## Scope

This policy applies to:

- The **whitepaper** and its described scheme (mathematical construction, workflows, and assumptions).  
- Any **reference implementations or tools** that may eventually live in this repository (for example, calculators or worksheet generators).

It does **not** cover third-party wallets, websites, or services that may implement or integrate Schiavinato Sharing independently.

---

## How to Report a Security Issue

If you believe you have found:

- a flaw in the **scheme design** or threat model,  
- an implementation bug that could lead to **loss of funds** or **undetected share misuse**, or  
- documentation that could realistically mislead users into unsafe behavior,

please:

1. **Do not** post full technical details in a public GitHub issue or discussion immediately.  
2. Instead, contact the maintainers via the contact information provided in the project’s GitHub profile or repository description (for example, a security-focused email address, once published).  
3. Provide a clear, concise description that includes:
   - What you found and why you believe it is a problem.  
   - Which components are affected (whitepaper section, tool, workflow, etc.).  
   - Any minimal examples or steps needed to reproduce the issue.

If no dedicated security contact is listed yet, you may open a **high-level** GitHub issue describing the area of concern (e.g., “possible workflow that leaks information about passphrases”), and ask for a private communication channel to share details.

---

## Expectations and Responsible Disclosure

We aim to:

- Acknowledge receipt of your report in a reasonable time.  
- Investigate the issue and, where appropriate, prepare fixes or clarifications.  
- Coordinate on timing before publishing detailed information about the vulnerability, especially if users need time to adjust their setups.

In turn, we ask reporters to:

- Give maintainers a reasonable window to understand and address the issue before public disclosure.  
- Avoid attempting to exploit vulnerabilities on live systems or against real users.  
- Refrain from requesting or accepting custody of real user secrets (mnemonics, shares, passphrases) as part of any proof-of-concept.

This last point reflects a core principle of Schiavinato Sharing and GRIFORTIS: **specialists may advise, but users retain control of secrets.**

---

## Implemented Security Hardening

The Schiavinato Sharing implementations include defense-in-depth security measures:

### Timing Attack Prevention

All implementations use **constant-time comparison** for checksum validation to prevent timing side-channel attacks:

- **Row checksums**: Compared using bitwise XOR without early exit
- **Master checksum**: Validated with constant-time equality checks  
- **BIP39 checksums**: Binary string comparison in constant time

This prevents attackers with precise timing measurement capability from inferring information about secret values through execution time differences.

**Risk Context**: Timing attacks require nanosecond-level measurement precision and are primarily relevant in network-accessible environments. For air-gapped offline usage, this risk is minimal but the protection is provided regardless.

### Memory Dump Protection

All implementations explicitly wipe sensitive data from memory after use:

- **Word indices**: Zeroed immediately after share generation
- **Polynomial coefficients**: Cleared after polynomial evaluation
- **Recovered secrets**: Overwritten after validation completes

While garbage collection will eventually reclaim memory, explicit cleanup reduces the window of vulnerability to memory dump attacks (e.g., from malware with memory access or forensic analysis).

**Implementation Details**:
- Arrays are overwritten with zeros element-by-element
- Cleanup occurs in `finally` blocks to ensure execution even on errors
- TypeScript library: `secureWipeArray()` and `secureWipeNumber()` functions
- HTML tool: `secureWipeArray()` JavaScript function

**Risk Context**: Memory dump attacks require either physical access to the device or the presence of malware with memory inspection capabilities. Air-gapped environments are inherently more resistant to these threats, but the protections are implemented as best practice.

### Cryptographic Randomness

Both implementations use cryptographically secure random number generation:

- **Browser/HTML tool**: `window.crypto.getRandomValues()`
- **Node.js library**: `crypto.randomFillSync()` (via `@noble/hashes`)
- **Fallback validation**: Throws error if secure randomness is unavailable

**No use of `Math.random()`** anywhere in share generation or key material creation.

### Transparency

These security features are:
- **Automatic**: Work without user configuration
- **Transparent**: Do not affect test vectors or share compatibility
- **Auditable**: Documented in source code comments
- **Tested**: Covered by automated test suites

---

## Non-Security Feedback

For general questions, suggestions, or non-sensitive bug reports (typos, minor formatting issues, unclear explanations), please use the standard GitHub issue tracker instead of the security contact. This helps keep security channels focused on time-sensitive and high-impact concerns.


