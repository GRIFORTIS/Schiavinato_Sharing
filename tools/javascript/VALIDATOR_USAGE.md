# JS Library Validator - Usage Guide

## Overview

The **JS Library Validator** is an HTML-based testing tool designed to validate and test the Schiavinato Sharing JavaScript library. It provides a comprehensive user interface for:

- Generating valid BIP39 mnemonics and IDs (auditors can later mutate shares to simulate invalid cases)
- Creating Shamir shares using the library
- Recovering mnemonics from shares
- Validating checksums and BIP39 compliance

**⚠️ IMPORTANT: This is a TESTING TOOL ONLY. Do NOT use it for real cryptocurrency wallets.**

## Getting Started

### Prerequisites

1. **Build the library** first:
   ```bash
   cd tools/javascript
   npm install
   npm run build
   ```

2. **Verify the build** created these files:
   - `dist/index.mjs` (ES Module)
   - `dist/index.js` (CommonJS)
   - `dist/index.d.ts` (TypeScript definitions)

### Running the Validator

1. Open `JS_Library_Validator.html` in a modern web browser
2. The tool will automatically import the library from `./dist/index.mjs`
3. Read and accept the disclaimer to start testing

**Browser Requirements:**
- Modern browser with ES Module support (Chrome 91+, Firefox 89+, Safari 15+, Edge 91+)
- JavaScript enabled
- No internet connection required (library loads locally)

## Features & Usage

### 1. Configure Shares

Set the parameters for the Shamir secret sharing scheme:

- **Threshold (K)**: Number of shares required for recovery (2-5)
- **Number of Shares (N)**: Total shares to generate (2-16, must be ≥ K)

**Examples:**
- 2-of-3: K=2, N=3 (any 2 shares can recover)
- 3-of-5: K=3, N=5 (any 3 shares can recover)

### 2. Create Shares

#### Generate Test Seeds

The validator provides four always-valid seed generators:

1. **New 24 Words (Valid)** - Generates a 24-word mnemonic with a valid BIP39 checksum
2. **New 12 Words (Valid)** - Generates a 12-word mnemonic
3. **New 24 IDs (Valid)** - Generates 24 BIP39 indices (0-2047)
4. **New 12 IDs (Valid)** - Generates 12 indices

All four buttons call the same secure generation routine; no invalid mnemonics are produced here. Auditors can still test failure cases using the “Break BIP39 on Share” control described later.

#### Manual Input

You can also manually enter or paste:
- BIP39 words (space, comma, or newline separated)
- Word indices 0-2047 (space, comma, or newline separated)
- Mixed format

**Supported Word Counts:** 12 or 24 words only

#### Create Shares Button

Click "Create Shares" to:
1. Validate the input mnemonic
2. Generate N shares using the configured K-of-N scheme
3. Populate all share containers with word and ID formats

### 3. Schiavinato Shares

After creating shares, you'll see N share containers (dynamically generated based on your N value).

#### Each Share Contains:

1. **Header**:
   - Share number (e.g., "Share 1 of 3")
   - Checkbox: "Use this Share for Recovery"

2. **Three Columns**:
   - **Labels**: Shows the structure (Master Verification, Words 1-24, Checksums C1-C8)
   - **Words Field**: Share data in word format (or `[ID]` for share values)
   - **IDs Field**: Share data in numeric ID format (0-2052)

3. **Copy Button**: Copies the share in "Word - ID" format to clipboard

#### Auditor Control (Share #1)

- Share #1 includes a **“Break BIP39 on Share”** button.
- Clicking it mutates the first share by adding 1 (mod 2053) to the 1st, 2nd, and 5th ID entries.
- Both the IDs and Words fields update automatically so the change is fully visible.
- Use this only for auditing/testing; it will intentionally make the recovered mnemonic fail the BIP39 checksum while keeping the share structure valid.

#### Share Selection

- First K shares are checked by default
- Check/uncheck shares to select which ones to use for recovery
- **Visual Feedback**:
  - **Grey/Yellow**: Valid selection (K or fewer shares selected)
  - **Red**: Invalid selection (more than K shares selected)

### 4. Recover Wallet

#### Recovery Process

1. Select exactly K shares using the checkboxes
2. Ensure each selected share has data in either the Words or IDs field
3. Click "Recover Wallet"

#### What Happens

The validator will:
1. **Parse** the K selected shares
2. **Interpolate** using Lagrange interpolation to recover word indices
3. **Validate** row checksums (C1-C8)
4. **Validate** master verification number
5. **Validate** BIP39 checksum
6. **Display** the recovered mnemonic

#### Success

If recovery is successful:
- Recovered mnemonic appears in the "Recovered Wallet" field
- Green "✓ Recovery successful!" message appears
- BIP39 checksum validation passes

#### Errors

If recovery fails, you'll see detailed error information:
- **Row checksum errors**: Indicates which rows (C1-C8) failed validation
- **Master verification failed**: Overall checksum mismatch
- **BIP39 checksum invalid**: Recovered mnemonic doesn't pass BIP39 validation
- **Generic errors**: Structural problems with shares

### 5. Recovered Wallet

#### Features

- **Multi-line display**: Shows recovered words one per line
- **Validation status**: Shows whether BIP39 checksum is VALID or INVALID
- **Validate Button**: Re-validates the BIP39 checksum
- **Clean Button**: Clears the recovered wallet display

## Testing Scenarios

### Test Case 1: Basic Round-Trip (Happy Path)

1. Generate a valid 12-word seed
2. Configure K=2, N=3
3. Create shares
4. Select shares 1 and 2
5. Recover wallet
6. **Expected**: Successful recovery with valid BIP39 checksum

### Test Case 2: Auditor – Break BIP39

1. Generate a valid 12-word seed
2. Configure K=2, N=3
3. Create shares
4. Click “Break BIP39 on Share” inside Share #1
5. Select shares 1 and 2
6. Recover wallet
7. **Expected**: Recovery completes structurally, but BIP39 checksum fails

### Test Case 3: Different Share Combinations

1. Generate a valid 24-word seed
2. Configure K=2, N=3
3. Create shares
4. Test recovery with:
   - Shares {1, 2}
   - Shares {1, 3}
   - Shares {2, 3}
5. **Expected**: All combinations recover the same mnemonic

### Test Case 4: Higher Threshold

1. Generate a valid 12-word seed
2. Configure K=3, N=5
3. Create shares
4. Select any 3 shares
5. Recover wallet
6. **Expected**: Successful recovery with any 3 shares

### Test Case 5: Insufficient Shares

1. Generate a valid 12-word seed
2. Configure K=3, N=5
3. Create shares
4. Select only 2 shares
5. Attempt recovery
6. **Expected**: Error message "Please select exactly 3 shares"

### Test Case 6: Corrupted Share

1. Generate a valid 12-word seed
2. Configure K=2, N=3
3. Create shares
4. Manually edit one word in Share 1 (corrupt it)
5. Select shares 1 and 2
6. Recover wallet
7. **Expected**: Row checksum error or master verification failure

## Troubleshooting

### Library Not Loading

**Error**: "SchiavinatoSharing is not defined"

**Solution**:
1. Ensure you've built the library (`npm run build`)
2. Check that `dist/index.mjs` exists
3. Verify the import path in the HTML file matches your build location

### Module Import Error

**Error**: "Failed to resolve module specifier"

**Solution**:
1. Serve the files via HTTP (not file://)
2. Use a local server: `python3 -m http.server 8000`
3. Or use VS Code Live Server extension

### Recovery Fails with Valid Shares

**Possible Causes**:
1. Shares from different split operations mixed together
2. Share data was modified/corrupted
3. Wrong word count selected during recovery

**Solution**:
1. Regenerate shares from scratch
2. Ensure all shares are from the same split operation
3. Verify K and N configurations haven't changed

### BIP39 Validation Fails

This is expected when:
1. You intentionally used the **“Break BIP39 on Share”** auditor control
2. Shares were corrupted during copy/paste
3. Recovery detected errors in checksums

## Technical Details

### Library Integration

The validator imports the library using ES modules:

```javascript
import * as SS from './dist/index.mjs';
window.SchiavinatoSharing = SS;
```

### Functions Used

From the library, the validator uses:

- `generateValidMnemonic(wordCount)` - Creates valid test seeds
- `mnemonicToIndices(mnemonic)` - Converts words to indices
- `indicesToMnemonic(indices)` - Converts indices to words
- `parseInput(text)` - Parses various input formats
- `splitMnemonic(mnemonic, k, n)` - Creates shares
- `recoverMnemonic(shares, wordCount)` - Recovers from shares
- `validateBip39Mnemonic(mnemonic)` - Validates BIP39 checksum

### Data Flow

```
Generate Seed
    ↓
Parse Input (words/IDs)
    ↓
Split Mnemonic → N Shares
    ↓
Display Shares (words + IDs)
    ↓
Select K Shares
    ↓
Recover Mnemonic
    ↓
Validate Checksums
    ↓
Display Result
```

## Best Practices

### For Testing

1. **Start Simple**: Begin with 2-of-3 and 12 words
2. **Test All Combinations**: For 2-of-3, test all {1,2}, {1,3}, {2,3}
3. **Test Error Cases**: Use the “Break BIP39 on Share” button to verify error detection
4. **Vary Parameters**: Test different K and N values
5. **Test Both Formats**: Try both word and ID inputs

### For Demonstration

1. Use the validator to show how the library works
2. Demonstrate error detection with the Break BIP39 auditor control
3. Show that any K shares work for recovery
4. Explain the additional Schiavinato checksums

### Data Safety

1. **Never** use generated seeds for real wallets
2. **Never** test with real cryptocurrency mnemonics
3. Clear all fields after testing
4. Close the browser when done

## Limitations

### Current Limitations

1. **Word Count**: Only supports 12 and 24 words (not 15, 18, 21)
2. **No Persistence**: Data is lost on page refresh
3. **Single Session**: Cannot save/load share sets
4. **Browser Only**: Requires modern browser with ES module support
5. **Local Only**: Library must be built locally (or use CDN when published)

### Not Included (Left for v2)

1. Debug view of Shamir coefficients
2. Display of Lagrange coefficients used in recovery
3. Step-by-step recovery visualization
4. Share export/import functionality
5. Print-friendly layouts

## Support

For issues with the validator:

1. **Check Console**: Open browser DevTools (F12) and check for errors
2. **Verify Build**: Ensure library is built correctly
3. **Test Library Directly**: Run `npm test` in the library directory
4. **Report Issues**: Open a GitHub issue with:
   - Browser version
   - Error messages from console
   - Steps to reproduce

## Version History

### v0.1.0 (2025-11-23)
- Initial release
- Basic create/recover functionality
- Valid seed generation plus auditor-only BIP39 breaker
- Visual feedback for share selection
- BIP39 checksum validation
- 12 and 24 word support

## Future Enhancements

Planned for future versions:

1. **v0.2.0**:
   - Debug view with coefficients
   - Lagrange multiplier display
   - Step-by-step recovery visualization

2. **v0.3.0**:
   - Share export/import (JSON format)
   - Print-friendly layouts
   - Dark/Light theme toggle

3. **v0.4.0**:
   - Support for 15, 18, 21 word mnemonics (if library adds support)
   - Advanced validation options
   - Performance metrics

---

**Remember**: This is a testing tool. Never use it for production or real cryptocurrency wallets!

