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

## Non-Security Feedback

For general questions, suggestions, or non-sensitive bug reports (typos, minor formatting issues, unclear explanations), please use the standard GitHub issue tracker instead of the security contact. This helps keep security channels focused on time-sensitive and high-impact concerns.


