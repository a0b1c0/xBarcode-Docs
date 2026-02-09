# Data Matrix Encoding Algorithm

This document details the implementation principles, key algorithms, and fixed issues of the Data Matrix ECC 200 encoder.

## Table of Contents

1. [Overview](#overview)
2. [Encoding Process](#encoding-process)
3. [Symbol Size Selection](#symbol-size-selection)
4. [Encoding Modes](#encoding-modes)
5. [Reed-Solomon Error Correction](#reed-solomon-error-correction)
6. [Bit Placement Algorithm](#bit-placement-algorithm)
7. [Multi-Region Symbol Handling](#multi-region-symbol-handling)
8. [Critical Issues Fixed](#critical-issues-fixed)

---

## Overview

Data Matrix is a two-dimensional matrix barcode widely used for small item identification. We implement the **ECC 200** version, which uses Reed-Solomon error correction and supports a data capacity of up to approximately 2,335 bytes.

### Key Features

| Feature | Value |
|---------|-------|
| Error Correction Type | Reed-Solomon (GF 256, Polynomial 0x12D) |
| Min Size | 10×10 modules |
| Max Size | 144×144 modules |
| Supported Modes | ASCII, C40, Text, Base256 |

---

## Encoding Process

```
Input String
    ↓
UTF-8 Byte Conversion
    ↓
Mode Selection (ASCII/C40/Text/Base256)
    ↓
Generate Data Codewords
    ↓
Add Padding Codewords (PAD = 129)
    ↓
Reed-Solomon Error Correction Calculation
    ↓
Codeword Interleaving (Multi-block symbols)
    ↓
Bit Placement (DefaultPlacement)
    ↓
Physical Matrix Mapping (Add finder patterns)
    ↓
SVG Output
```

---

## Symbol Size Selection

Symbol size is automatically selected based on data length, defined in the `PROD_SYMBOLS` array in `constants.rs`.

### Common Symbol Sizes

| Symbol Size | Data Region | Data Capacity | ECC Codewords | Regions | Interleaved Blocks |
|-------------|-------------|---------------|---------------|---------|--------------------|
| 10×10 | 8×8 | 3 | 5 | 1×1 | 1 |
| 22×22 | 20×20 | 30 | 20 | 1×1 | 1 |
| 32×32 | 28×28 | 62 | 36 | 2×2 | 1 |
| 52×52 | 48×48 | 204 | 84 | 2×2 | 2 |
| 64×64 | 56×56 | 280 | 112 | 4×4 | 2 |
| 72×72 | 64×64 | 368 | 144 | 4×4 | 4 |

---

## Encoding Modes

### ASCII Mode (Default)

```rust
// Single char: Codeword = ASCII value + 1
'A' (65) → Codeword 66
' ' (32) → Codeword 33
':' (58) → Codeword 59

// Double Digit Optimization: Two consecutive digits encode to one codeword
"42" → Codeword 130 + 42 = 172
```

### C40 Mode

Suitable for uppercase-heavy content. 3 characters encode to 2 codewords.

- Latch Codeword: 230
- Charset: Space, 0-9, A-Z

### Text Mode

Suitable for lowercase-heavy content. 3 characters encode to 2 codewords.

- Latch Codeword: 239
- Charset: Space, 0-9, a-z

### Base256 Mode

Used for binary data or non-ASCII characters (e.g., Chinese).

- Latch Codeword: 231
- Random Length Indicator (RLI) included

---

## Reed-Solomon Error Correction

### Galois Field

```
GF(256), Generator Polynomial: 0x12D (301)
Primitive Element: α = 2
```

### Block Extraction (Critical!)

For multi-block symbols, use **Stepped Extraction** instead of continuous slicing:

```rust
// ❌ Incorrect Way (Previous bug)
Block 0 = codewords[0..140]
Block 1 = codewords[140..280]

// ✓ Correct Way (ISO 16022)
Block 0 = codewords[0, 2, 4, 6, ...]  // Even indices
Block 1 = codewords[1, 3, 5, 7, ...]  // Odd indices
```

### Interleaved Output

```
Final Codewords = [Original Data Codewords] + [Interleaved ECC Codewords]
ECC Interleaving: Block0_ECC[0], Block1_ECC[0], Block0_ECC[1], Block1_ECC[1], ...
```

---

## Bit Placement Algorithm

Bit placement uses the `DefaultPlacement` algorithm defined in ISO 16022 Annex M.1.

### Utah Shape

The 8 bits of each codeword are placed in a "Utah" shape:

```
    ┌─┬─┐
    │1│2│
  ┌─┼─┼─┤
  │3│4│5│
  ├─┼─┼─┤
  │6│7│8│
  └─┴─┴─┘
```

### Scanning Order

Starts from (row=4, col=0), scanning alternatively diagonally up-right and down-left, handling boundary wrapping and the four special corner cases (corner1-4).

---

## Multi-Region Symbol Handling

For large symbols (e.g., 64×64), the data region is split into multiple sub-regions, each with its own finder patterns.

### Physical Matrix Structure (e.g., 64×64)

```
┌────────────────┬────────────────┬────────────────┬────────────────┐
│  Region 0,0    │  Region 0,1    │  Region 0,2    │  Region 0,3    │
│  (14×14 Data)  │  (14×14 Data)  │  (14×14 Data)  │  (14×14 Data)  │
│  + finder rim  │  + finder rim  │  + finder rim  │  + finder rim  │
├────────────────┼────────────────┼────────────────┼────────────────┤
│  Region 1,0    │      ...       │      ...       │  Region 1,3    │
├────────────────┼────────────────┼────────────────┼────────────────┤
│  Region 2,0    │      ...       │      ...       │  Region 2,3    │
├────────────────┼────────────────┼────────────────┼────────────────┤
│  Region 3,0    │      ...       │      ...       │  Region 3,3    │
└────────────────┴────────────────┴────────────────┴────────────────┘
```

### Logical to Physical Mapping

```rust
// Logical coords (global_row, global_col) → Physical coords (phys_x, phys_y)
let region_r = global_row / data_region_height;
let region_c = global_col / data_region_width;
let local_row = global_row % data_region_height;
let local_col = global_col % data_region_width;

let base_x = region_c * (data_region_width + 2);
let base_y = region_r * (data_region_height + 2);

let phys_x = base_x + 1 + local_col;  // +1 for left finder
let phys_y = base_y + 1 + local_row;  // +1 for top finder
```

---

## Critical Issues Fixed

### Issue 1: Large Symbol Decoding Failure

**Symptom**: Data Matrix larger than ~200 chars failed to decode significantly.

**Root Cause**: RS block extraction used continuous slicing instead of stepped extraction.

**Fix**:
```rust
// Before (Incorrect)
let start = block * data_per_block;
let block_data = codewords[start..start + data_per_block].to_vec();

// After (Correct)
let mut block_data = Vec::new();
let mut d = block;
while d < codewords.len() {
    block_data.push(codewords[d]);
    d += blocks;  // Stepped extraction
}
```

**Impact**: 52×52 and larger symbols.

### Issue 2: Encoding Mode Selection

**Symptom**: Suboptimal efficiency.

**Solution**: implemented DP (Dynamic Programming) to select the optimal encoding mode (ASCII/C40/Text).

---

## References

- ISO/IEC 16022:2006 - Data Matrix Barcode Symbology Specification
- [ZXing Reference Implementation](https://github.com/zxing/zxing)
- [Data Matrix Wikipedia](https://en.wikipedia.org/wiki/Data_Matrix)

---

*Doc Version: 2024-12-22*
*Last Update: Fixed RS block extraction issue*
