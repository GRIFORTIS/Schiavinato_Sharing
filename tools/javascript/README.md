# Schiavinato Sharing - JavaScript/TypeScript Library

Human-executable secret sharing for BIP39 mnemonics using GF(2053).

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.3-blue)](https://www.typescriptlang.org/)
[![Node.js](https://img.shields.io/badge/Node.js-%3E%3D18-green)](https://nodejs.org/)

## Overview

The **Schiavinato Sharing** scheme is an extension of Shamir's Secret Sharing specifically designed for BIP39 cryptocurrency mnemonics. It operates over the finite field GF(2053) and includes additional checksums that enable:

- **Error Detection**: Identify corrupted shares during recovery
- **Manual Recovery**: Perform recovery calculations by hand using printed lookup tables
- **Cryptographic Security**: Based on proven Shamir Secret Sharing
- **Human-Friendly**: Uses numbers small enough for mental arithmetic (0-2052)

### Key Features

- ✅ **TypeScript Native**: Full type safety and IntelliSense support
- ✅ **Zero Dependencies**: Core library has only 2 audited dependencies
- ✅ **Thoroughly Tested**: 100% test coverage with test vectors
- ✅ **Browser & Node.js**: Works in all JavaScript environments
- ✅ **Auditable**: Extensively commented source code
- ✅ **BIP39 Compliant**: Validates mnemonics using industry-standard library

## Installation

```bash
npm install @grifortis/schiavinato-sharing
```

Or with yarn:

```bash
yarn add @grifortis/schiavinato-sharing
```

Or with pnpm:

```bash
pnpm add @grifortis/schiavinato-sharing
```

## Quick Start

### Split a Mnemonic

```typescript
import { splitMnemonic } from '@grifortis/schiavinato-sharing';

const mnemonic = 'spin result brand ahead poet carpet unusual chronic denial festival toy autumn';

// Create 3 shares, any 2 required for recovery (2-of-3 scheme)
const shares = await splitMnemonic(mnemonic, 2, 3);

console.log(shares[0]);
// {
//   shareNumber: 1,
//   wordShares: [82, 1572, 1342, ...],
//   checksumShares: [154, 555, 751, 1410],
//   masterVerificationShare: 1819
// }
```

### Recover a Mnemonic

```typescript
import { recoverMnemonic } from '@grifortis/schiavinato-sharing';

// Use any 2 of the 3 shares
const result = await recoverMnemonic([shares[0], shares[1]], 12);

if (result.success) {
  console.log('Recovered:', result.mnemonic);
} else {
  console.error('Errors:', result.errors);
}
```

## API Reference

### Main Functions

#### `splitMnemonic(mnemonic, k, n, options?)`

Splits a BIP39 mnemonic into n shares with k-threshold.

**Parameters:**
- `mnemonic: string` - The BIP39 mnemonic phrase (12 or 24 words)
- `k: number` - Threshold: minimum shares required for recovery (≥ 2)
- `n: number` - Total number of shares to generate (< 2053)
- `options?: SplitOptions` - Optional configuration
  - `wordlist?: string[]` - Custom BIP39 wordlist (defaults to English)

**Returns:** `Promise<Share[]>` - Array of share objects

**Example:**
```typescript
// 3-of-5 scheme for 24-word mnemonic
const shares = await splitMnemonic(mnemonic24, 3, 5);
```

---

#### `recoverMnemonic(shares, wordCount, options?)`

Recovers a mnemonic from shares and validates checksums.

**Parameters:**
- `shares: ShareData[]` - Array of share objects (minimum k shares)
- `wordCount: 12 | 24` - Expected word count of the original mnemonic
- `options?: RecoverOptions` - Optional configuration
  - `wordlist?: string[]` - Custom BIP39 wordlist
  - `strictValidation?: boolean` - Validate BIP39 checksum (default: true)

**Returns:** `Promise<RecoveryResult>` - Recovery result with mnemonic and error details

**Example:**
```typescript
const result = await recoverMnemonic([share1, share2], 12);

if (result.success) {
  console.log('Mnemonic:', result.mnemonic);
} else {
  if (result.errors.row.length > 0) {
    console.error('Row checksum errors in rows:', result.errors.row);
  }
  if (result.errors.master) {
    console.error('Master checksum failed');
  }
  if (result.errors.bip39) {
    console.error('BIP39 checksum failed');
  }
}
```

---

#### `computeLagrangeMultipliers(shareNumbers)`

Computes Lagrange multipliers for manual recovery.

**Parameters:**
- `shareNumbers: number[]` - Array of share numbers to use (e.g., [1, 2])

**Returns:** `number[]` - Array of multipliers in GF(2053)

**Example:**
```typescript
import { computeLagrangeMultipliers } from '@grifortis/schiavinato-sharing';

// For manual recovery with shares 1 and 2
const multipliers = computeLagrangeMultipliers([1, 2]);
// Returns [2, 2052]

// Manual recovery formula:
// secret = (multipliers[0] * share1Value + multipliers[1] * share2Value) mod 2053
```

---

#### `validateBip39Mnemonic(mnemonic, wordlist?)`

Validates a BIP39 mnemonic (re-exported from `@scure/bip39`).

**Parameters:**
- `mnemonic: string` - The mnemonic to validate
- `wordlist?: string[]` - Custom wordlist (defaults to English)

**Returns:** `boolean` - True if valid

**Example:**
```typescript
import { validateBip39Mnemonic } from '@grifortis/schiavinato-sharing';

if (validateBip39Mnemonic(mnemonic)) {
  console.log('Valid BIP39 mnemonic');
}
```

### TypeScript Types

```typescript
interface Share {
  shareNumber: number;
  wordShares: number[];
  checksumShares: number[];
  masterVerificationShare: number;
}

interface RecoveryResult {
  mnemonic: string | null;
  errors: {
    row: number[];        // Row indices with checksum errors
    master: boolean;      // Master checksum failed
    bip39: boolean;       // BIP39 checksum failed
    generic: string | null; // Generic error message
  };
  success: boolean;
  sharesWithInvalidChecksums?: Set<number>;
}

interface SplitOptions {
  wordlist?: string[];
}

interface RecoverOptions {
  wordlist?: string[];
  strictValidation?: boolean;
}
```

## Advanced Usage

### Field Arithmetic

Access low-level GF(2053) operations:

```typescript
import { mod, modAdd, modSub, modMul, modInv, FIELD_PRIME } from '@grifortis/schiavinato-sharing';

console.log(FIELD_PRIME); // 2053

console.log(modAdd(2000, 100));  // 47
console.log(modSub(100, 200));   // 1953
console.log(modMul(100, 50));    // 841
console.log(modInv(2));          // 1027
```

### Polynomial Operations

For testing and verification:

```typescript
import { randomPolynomial, evaluatePolynomial } from '@grifortis/schiavinato-sharing';

// Create a degree-1 polynomial with secret 1679
const poly = randomPolynomial(1679, 1);
// Returns [1679, randomCoefficient]

// Evaluate at x=1
const share1 = evaluatePolynomial(poly, 1);
```

### Checksum Functions

Compute Schiavinato checksums:

```typescript
import { computeRowChecks, computeMasterCheck } from '@grifortis/schiavinato-sharing';

const wordIndices = [1679, 1470, 216, 41, 1337, 278, 1906, 323, 467, 681, 1843, 125];

const rowChecks = computeRowChecks(wordIndices);
// Returns [1312, 1656, 643, 596]

const masterCheck = computeMasterCheck(wordIndices);
// Returns 101
```

### Custom Random Source (for Testing)

```typescript
import { configureRandomSource } from '@grifortis/schiavinato-sharing';

// Deterministic random for testing
let counter = 0;
configureRandomSource({
  getRandomValues(array: Uint32Array) {
    for (let i = 0; i < array.length; i++) {
      array[i] = counter++ % 2053;
    }
  }
});
```

## Security Considerations

### Random Number Generation

The library uses cryptographically secure random number generation:
- **Browser**: `window.crypto.getRandomValues()`
- **Node.js**: `crypto.randomFillSync()`

Ensure your environment provides a secure random source.

### Share Storage

- **Never store k or more shares together** - this defeats the purpose of secret sharing
- Store shares in different physical locations
- Consider encrypting shares with passwords for additional security
- Print shares on paper for long-term storage (backup strategy)

### Threshold Selection

Choose your threshold (k) carefully:
- **2-of-3**: Good balance between security and usability
- **3-of-5**: More redundancy, lower risk of permanent loss
- **2-of-2**: Maximum security, but no redundancy

### Validation

Always validate recovered mnemonics:
```typescript
const result = await recoverMnemonic(shares, 12);

if (!result.success) {
  // Check what went wrong
  if (result.errors.bip39) {
    console.error('BIP39 checksum failed - recovered mnemonic is invalid');
  }
  if (result.errors.row.length > 0) {
    console.error('Row checksums failed - some shares may be corrupted');
  }
}
```

### Security Hardening Features

The library implements security best practices to protect against advanced attacks:

#### Constant-Time Comparisons

All checksum validations use constant-time comparison to prevent timing attacks:
- Row checksum validation uses bitwise operations without early exit
- Master checksum validation uses constant-time equality checks
- BIP39 checksum validation compares strings in constant time

This prevents attackers from inferring information about secret values through execution time differences.

#### Automatic Memory Cleanup

Sensitive data is explicitly wiped from memory after use:
- Word indices are zeroed after share generation
- Polynomial coefficients are cleared after evaluation
- Recovered secrets are wiped after validation

While JavaScript's garbage collector will eventually reclaim memory, explicit cleanup reduces the window of vulnerability to memory dump attacks.

**Example - Using security utilities:**
```typescript
import { constantTimeEqual, secureWipeArray } from '@grifortis/schiavinato-sharing';

// Constant-time comparison
if (constantTimeEqual(checksum1, checksum2)) {
  console.log('Valid');
}

// Explicit memory cleanup
const sensitiveData = [1679, 456, 892];
// ... use the data ...
secureWipeArray(sensitiveData); // Now [0, 0, 0]
```

**Note**: These security features are transparent to normal usage - they work automatically without requiring any special configuration.

## Browser Usage

### Via npm/bundler

```typescript
import { splitMnemonic, recoverMnemonic } from '@grifortis/schiavinato-sharing';

// Use as shown in examples above
```

### Via CDN (UMD)

```html
<script src="https://unpkg.com/@grifortis/schiavinato-sharing/dist/browser/index.global.js"></script>
<script>
  const { splitMnemonic, recoverMnemonic } = SchiavinatoSharing;
  
  // Use the library
  splitMnemonic(mnemonic, 2, 3).then(shares => {
    console.log('Shares:', shares);
  });
</script>
```

## Testing

### Automated Tests

The library includes comprehensive tests:

```bash
# Run tests
npm test

# Run tests in watch mode
npm run test:watch

# Generate coverage report
npm run test:coverage
```

### Manual Testing with Validator

Use the included HTML validator for manual testing and demonstration:

1. Build the library: `npm run build`
2. Open `JS_Library_Validator.html` in a browser
3. Follow the on-screen instructions

See [VALIDATOR_USAGE.md](./VALIDATOR_USAGE.md) for the complete testing guide.

### Test Vectors

The library is tested against the canonical test vectors from `TEST_VECTORS.md`:

- **Mnemonic**: `spin result brand ahead poet carpet unusual chronic denial festival toy autumn`
- **Scheme**: 2-of-3
- **All combinations verified**: {1,2}, {1,3}, {2,3}
- **Lagrange coefficients verified**
- **Checksums validated**

## JS Library Validator

A comprehensive HTML-based testing tool is included to validate and test the library:

**Location**: `JS_Library_Validator.html`

**Features**:
- Generate valid BIP39 mnemonics/IDs (12 or 24 words) with an auditor-only “Break BIP39 on Share” button for edge-case testing
- Create Shamir shares using the library
- Recover mnemonics from shares
- Validate row checksums and master verification
- Validate BIP39 checksums
- Test different K-of-N configurations

**Usage**:
```bash
# 1. Build the library
npm run build

# 2. Open JS_Library_Validator.html in your browser
# The validator will automatically import the library

# 3. Or serve via HTTP
python3 -m http.server 8000
# Then open http://localhost:8000/JS_Library_Validator.html
```

**Documentation**: See [VALIDATOR_USAGE.md](./VALIDATOR_USAGE.md) for detailed usage guide.

**⚠️ Important**: The validator is for TESTING ONLY. Never use it with real cryptocurrency mnemonics.

## Comparison with HTML Tool

This JavaScript library is extracted from the [HTML tool](../html/schiavinato_sharing.html):

| Feature | HTML Tool | JS Library | JS Validator |
|---------|-----------|------------|--------------|
| Environment | Air-gapped browser | Node.js / Browser | Browser (testing) |
| Dependencies | Embedded | `@scure/bip39`, `@noble/hashes` | Uses library |
| Use Case | One-time operations | Programmatic integration | Library testing |
| Format | Single HTML file | Modular TypeScript | Single HTML file |
| BIP39 | Embedded implementation | Industry-standard library | Uses library |
| Security | Maximum (air-gapped) | Production-ready | Testing only |

**When to use each:**
- **HTML Tool**: One-time split/recover operations, maximum security, no installation, production use
- **JS Library**: Integration into apps, automated workflows, testing, development
- **JS Validator**: Testing the library, demonstrating functionality, development validation

## License

MIT License - see [LICENSE](./LICENSE) file for details.

## Contributing

Contributions are welcome! Please see [CONTRIBUTING.md](../../CONTRIBUTING.md) for guidelines.

## References

- [Whitepaper](../../WHITEPAPER.md) - Full technical specification
- [Test Vectors](../../TEST_VECTORS.md) - Canonical test data
- [HTML Tool](../html/schiavinato_sharing.html) - Single-file implementation
- [BIP39 Specification](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki)

## Support

For issues, questions, or suggestions, please:
1. Check the [FAQ](../../WHITEPAPER.md#faq) in the whitepaper
2. Review existing [GitHub Issues](https://github.com/GRIFORTIS/Schiavinato_Sharing/issues)
3. Open a new issue with detailed information

## Acknowledgments

- **BIP39**: `@scure/bip39` by Paul Miller (@paulmillr)
- **SHA-256**: `@noble/hashes` by Paul Miller (@paulmillr)
- **Shamir Secret Sharing**: Adi Shamir (1979)
- **GF(2053) Design**: Schiavinato Sharing scheme

---

**⚠️ Important Security Notice**

This software is provided for educational and research purposes. Always:
- Test thoroughly before using with real cryptocurrency
- Verify recovered mnemonics before importing into wallets
- Keep backups of original mnemonics during testing
- Use the library in secure environments
- Review the source code and audit for your use case

**Use at your own risk. The authors assume no liability for lost funds.**

