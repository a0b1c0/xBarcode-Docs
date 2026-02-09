# xBarcode: High-Performance Rust Barcode Generator

[![Rust](https://img.shields.io/badge/Rust-1.83%2B-orange.svg)](https://www.rust-lang.org/)
[![Wasm](https://img.shields.io/badge/Wasm-Native-blue.svg)](https://webassembly.org/)
[![License](https://img.shields.io/badge/License-Proprietary-red.svg)](LICENSE)
[![Performance](https://img.shields.io/badge/Benchmark-4µs-green.svg)](https://xbarcode.ai/benchmarks)

**xBarcode** is a next-generation barcode generation engine written in pure Rust, optimized for WebAssembly (Wasm) and high-throughput server environments. It is engineered to be the **fastest** and **most memory-efficient** library in the Rust ecosystem.

> **Note**: This is the public documentation and issue tracker repository. The core engine is proprietary software available for commercial licensing and free via our [public API/Wasm demo](https://xbarcode.ai).

---

## 🚀 Why xBarcode?

### 1. Unmatched Performance (v1.5.4)
xBarcode utilizes a **Hybrid Architecture** (Fast-Path Mode + Dynamic Programming) to achieve generation speeds up to **6x faster** than leading alternatives.

| Symbology | Input Type | xBarcode | Competitor | Speedup |
| :--- | :--- | :--- | :--- | :--- |
| **QR Code** | Numeric | **4.2 µs** | 28 µs (fast_qr) | **6.7x** 🚀 |
| **QR Code** | Alphanumeric | **10.7 µs** | 39 µs (fast_qr) | **3.6x** 🚀 |
| **Data Matrix** | Alphanumeric | **1.4 µs** | 2.8 µs (rxing) | **2.0x** 🚀 |
| **Aztec** | Numeric | **1.5 µs** | 7.4 µs (rxing) | **4.9x** 🚀 |
| **PDF417** | Alphanumeric | **3.7 µs** | 7.4 µs (rxing) | **2.0x** 🚀 |
| **Code 128** | Standard | **78 µs** | 143 µs (rxing) | **1.8x** ⚡ |
| **EAN-13** | Product | **14.5 µs** | 41 µs (rxing) | **2.8x** 🚀 |
| **ITF** | Numeric | **17 µs** | 38 µs (rxing) | **2.2x** 🚀 |

*(Benchmark: Apple M4, Single-Threaded, Feb 2026)*

### 2. Zero-Allocation Philosophy
Unlike Java ports that rely on heavy garbage collection, xBarcode uses a strict **stack-allocation** and **buffer-reuse** strategy for the hot path.
*   **0** Unsafe Blocks (Memory Safe).
*   **0** Runtime Allocations for standard payloads.

### 3. Optimal Segmentation (Smaller Codes)
Speed doesn't mean larger codes. xBarcode employs a graph-based **Dynamic Programming (DP)** algorithm to find the absolute smallest physical barcode size for mixed data (e.g., Chinese + Numbers).
*   **xBarcode**: Version 3
*   **Competitors**: Version 4 (Larger, harder to scan)

---

## 🛠️ Supported Symbologies

| 2D Matrix | 1D Linear | Special |
| :--- | :--- | :--- |
| QR Code (Model 2) | Code 128 (A/B/C) | GS1 DataMatrix |
| Data Matrix (ECC200) | EAN-13 / UPC-A | GS1-128 |
| Aztec Code | Code 39 | Micro QR |
| PDF417 (Compact) | ITF-14 | WiFi / VCard |

---

## 📦 Usage

### Web / Wasm
Try the live demo at **[xbarcode.ai](https://xbarcode.ai)**. The Wasm binary is available for integration into enterprise web apps.

### Cloudflare Workers / Serverless
Optimized for the Edge. Zero cold-start latency (2ms).
*   **API**: `POST https://api.xbarcode.ai/v1/generate` (Contact for access)

---

## 📚 Documentation
Detailed technical documentation is available in the [`docs/`](docs/) directory.
*   [Engine Architecture](docs/architecture.md)
*   [Benchmark Analysis](docs/performance.md)

---

## 💬 Community & Support

*   **Issues**: Please use the [Issues](../../issues) tab to report bugs or request features.
*   **Discussion**: Join the conversation in [Discussions](../../discussions).
*   **License**: Copyright © 2026 xBarcode Team. All rights reserved.

---

*[xbarcode.ai](https://xbarcode.ai) - The Fastest is also the Best.*
