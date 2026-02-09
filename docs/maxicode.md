# MaxiCode Technical Documentation

## Overview

MaxiCode is a two-dimensional barcode symbology developed by UPS, primarily used for logistics tracking. It consists of a 30×33 hexagonal grid with a characteristic "bullseye" pattern in the center.

## Mode Classification

| Mode | Usage | Postal Code Format |
|------|-------|--------------------|
| **Mode 2** | UPS USA Domestic | 9-digit numeric (e.g., `902100000`) |
| **Mode 3** | UPS International | 1-6 alphanumeric chars (e.g., `SW1A1A`) |
| **Mode 4** | Standard Data | No structured routing |
| **Mode 5** | Enhanced ECC | No structured routing |
| **Mode 6** | Programming/Read | Special use |

---

## UPS Structured Carrier Message (SCM) Format

### Complete Format

```
[)>{RS}01{GS}96{GS}{POSTAL}{GS}{COUNTRY}{GS}{SERVICE}{GS}{DATA}
```

| Field | Description | Example |
|-------|-------------|---------|
| `[)>` | Header Identifier | Fixed |
| `{RS}` | Record Separator (0x1E) | Control Char |
| `01` | Format Type | Fixed |
| `{GS}` | Group Separator (0x1D) | Control Char |
| `96` | Year Code | Two digits |
| `{POSTAL}` | Postal Code | Mode 2: 9 numeric / Mode 3: ≤6 alphanumeric |
| `{COUNTRY}` | Country Code | 3 digits (e.g., 840=USA, 826=UK) |
| `{SERVICE}` | Service Class | 3 digits |
| `{DATA}` | Tracking Data | e.g., `1Z10427691UPSNK3W25807Z...` |

### Automatic Escaping

The JavaScript frontend automatically detects and escapes UPS format input:

```javascript
// User Input (No control chars)
[)>01969021000008400041Z95524105...

// Automatically converted to
[)>{RS}01{GS}96{GS}902100000{GS}840{GS}004{GS}1Z95524105...
```

---

## Encoding Process

### Primary Message (10 codewords)

The Primary Message contains routing information, packed using a 6-bit format:

**Mode 2 (Numeric Postal Code):**
```rust
cw[0] = ((postcode_num & 0x03) << 4) | 2  // mode=2 in lower 4 bits
cw[1] = (postcode_num >> 2) & 0x3F
cw[2] = (postcode_num >> 8) & 0x3F
cw[3] = (postcode_num >> 14) & 0x3F
cw[4] = (postcode_num >> 20) & 0x3F
cw[5] = ((postcode_num >> 26) & 0x0F) | ((postcode_len & 0x03) << 4)
cw[6] = ((postcode_len >> 2) & 0x0F) | ((country & 0x03) << 4)
cw[7] = (country >> 2) & 0x3F
cw[8] = ((country >> 8) & 0x03) | ((service & 0x0F) << 2)
cw[9] = (service >> 4) & 0x3F
```

**Mode 3 (Alphanumeric Postal Code):**
- Postal code chars converted to Code Set A values
- Padded/Truncated to 6 chars
- Uses a different bit layout

### Secondary Message (84/68 codewords)

The Secondary Message contains the actual data:
- Mode 2/3/4: 84 data codewords
- Mode 5: 68 data codewords (Enhanced ECC)

Encoding uses multiple character sets:
- **Set A**: Uppercase, Numeric, Space
- **Set B**: Lowercase
- **Set C/D/E**: Special characters

### Reed-Solomon Error Correction

| Region | Data | ECC |
|--------|------|-----|
| Primary | 10 codewords | 10 codewords |
| Secondary (Standard) | 84 codewords | 40 codewords (Split in two) |
| Secondary (Enhanced) | 68 codewords | 56 codewords (Split in two) |

---

## Matrix Generation

### Grid Mapping (Zint Formula)

```rust
let mod_seq = grid_value + 5;
let block = mod_seq / 6;
let bit_pos = 5 - (mod_seq % 6);  // MSB first
let codeword = codewords[block - 1];
let bit = (codeword >> bit_pos) & 1;
```

### Orientation Markers

Fixed black module positions:
- (0, 28), (0, 29) - Top Right Filler
- (9, 10), (9, 11), (10, 11) - Top Left Marker
- (15, 7), (16, 8) - Left Side Marker
- (16, 20), (17, 20) - Right Side Marker
- (22, 10), (23, 10) - Bottom Left Marker
- (22, 17), (23, 17) - Bottom Right Marker

---

## Debugging Guide

### Common Issues

1. **Scanned Result Has Duplicate Numbers**
    - Cause: Secondary Message includes routing info already present in Primary.
    - Solution: Secondary should only contain `header + data`, excluding `postal/country/service`.

2. **Scanner Failing to Recognize Mode 2/3**
    - Cause: Incorrect Matrix Indexing.
    - Checkpoint: Verify `grid_value + 5` and `block - 1` formulas.

3. **Data Truncation**
    - Cause: Exceeding capacity limits.
    - Mode 2/3/4: Max ~93 char capacity.
    - Check if NS compression is enabled.

### Debug Scripts

```bash
# Run debug script
cargo run --bin debug_maxicode_content

# Generate Test SVG
# Output: /tmp/maxi_real_ups.svg
```

### Verification Flow

1. Generate SVG file.
2. Scan with a mobile scanner app.
3. Compare scanned result with expected content.
4. Verify correct Primary/Secondary split.

---

## Reference Implementations

- **Zint**: `maxicode.c` - `mx_do_primary_2()`, `mx_do_primary_3()`
- **BWIP-JS**: JavaScript reference implementation
- **ISO 16023**: Official MaxiCode Specification

---

## File Structure (xBarcode Core)

```
xBarcode/src/symbology/maxicode/
├── mod.rs          # Module exports
├── encoder.rs      # Core encoding logic
├── parser.rs       # SCM format parsing
├── constants.rs    # Grid & Charset constants
└── ecc.rs          # Reed-Solomon ECC
```

## Changelog

- **2024-12-22**: Fixed Mode 2/3 Primary Message encoding.
- **2024-12-22**: Fixed Matrix indexing formula (grid+5, block-1).
- **2024-12-22**: Added Mode 3 International Postal Code support.
- **2024-12-22**: Added Frontend UPS SCM auto-escaping.
