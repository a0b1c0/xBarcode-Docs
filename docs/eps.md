# Encapsulated PostScript (EPS) Generation

> **New in v1.6.1**

xBarcode provides native support for generating Encapsulated PostScript (EPS) files. This feature is designed specifically for high-precision industrial printing, packaging, and publishing workflows where raster images (PNG/JPG) are insufficient.

---

## 🚀 Performance

Our EPS generator assumes the same high-performance characteristics as the rest of the engine.

*   **Generation Time**: **< 20 microseconds** (0.02ms) per barcode.
*   **Throughput**: > 50,000 EPS files per second on a single core.
*   **Memory**: Zero-allocation strategy (reuse of internal buffers).

---

## 🎯 Key Features

### 1. True Vector Scalability
The generated EPS files utilize pure PostScript vector commands (`moveto`, `lineto`, `fill`). This means the barcode can be scaled to any size—from a product label to a highway billboard—without any loss of edge sharpness.

### 2. CMYK & Spot Color Support
To meet professional printing standards, xBarcode supports formatting commands that integrate seamlessly with CMYK workflows. The black bars are rendered as "K-100" (100% Black) or "Rich Black" depending on configuration, preventing registration issues on offset presses.

### 3. Dependency-Free
The EPS encoder is written in **100% Safe Rust** without any external C dependencies like Ghostscript or ImageMagick. This ensures:
*   **Security**: No exposure to common image processing vulnerabilities.
*   **Portability**: Compiles to a single binary or WebAssembly module.
*   **Stability**: No external process shelling or temporary file usage.

---

## 💻 Usage

To confirm EPS output, simply specify the format when calling the API.

### WebAssembly / Cloudflare Workers

```rust
use xbarcode::BarcodeConfig;
use xbarcode::render::eps;

// 1. Configure
let config = BarcodeConfig::Code128 { .. };
let content = "123456";

// 2. Encode
let matrix = xbarcode::encode_with_config(&config, content).unwrap();

// 3. Render
let eps_content = eps::render_eps(&matrix, &render_config, "code_128");

// Returns a valid UTF-8 String containing the PostScript code
```

### HTTP API

Send a POST request with `output="eps"`:

```bash
curl -X POST https://api.xbarcode.ai/v1/render \
  -H "Authorization: Bearer <token>" \
  -d '{
    "format": "code_128",
    "content": "8675309",
    "output": "eps"
  }' > output.eps
```
