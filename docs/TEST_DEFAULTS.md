# Test Page Initialization Defaults

The following table lists the default initialization parameters used in the web test interface, along with industry-standard descriptions.

| Barcode Type | Description / Industry Usage | Default Width | Default Height | Initial Content |
|--------------|------------------------------|---------------|----------------|-----------------|
| **qr** | General purpose matrix code (URLs, payments, marketing) | 150 | 150 | `https://xbarcode.ai/` |
| **gs1_qr** | GS1-compliant QR Code (Supply chain, Digital Link) | 150 | 150 | `01012345678908000ABC\F1234https://xbarcode.ai` |
| **micro_qr** | Compact QR for small items (PCB, Electronics) | 100 | 100 | `1234-AAA-abc` |
| **datamatrix** | High-density industrial code (Electronics, Direct Part Marking) | 150 | 150 | `Data Matrix | Powered by https://xbarcode.ai` |
| **gs1_datamatrix** | GS1-compliant DataMatrix (Healthcare UDI, Pharmaceuticals) | 150 | 150 | `0101234567890987654321000ABCD1234\F123456` |
| **pdf417** | High-capacity stacked linear (ID Cards, Boarding Passes) | 300 | 100 | `Pdf 417 | Powered by https://xbarcode.ai` |
| **micro_pdf417** | Compact PDF417 (Small labels, specialized ID) | 200 | 100 | `MicroPDF417| by xBarcode` |
| **aztec** | Matrix code with center finder (Transport tickets, Mobile) | 150 | 150 | `Aztec Code | Powered by https://xbarcode.ai/` |
| **maxicode** (Auto) | UPS proprietary fixed-size code (Shipping labels) | 150 | 150 | `[)>01969000449118400121Z10427691UPSNK3W25807` |
| **maxicode** (Mode 2) | UPS US Default (Numeric Postal Code) | 150 | 150 | `[)>01 96900044911 840 012 1Z10427691 UPSN K3W258 07Z...` |
| **maxicode** (Mode 3) | UPS International (Alphanumeric Postal Code) | 150 | 150 | `[)>01 96902100000 840 004 1Z95524105 UPSN A66899 07Z...` |
| **maxicode** (Mode 4) | Standard Mode 4 (General Purpose) | 150 | 150 | `MaxiCode Mode 4 | Standard Data` |
| **maxicode** (Mode 5) | Standard Mode 5 (Enhanced Error Correction) | 150 | 150 | `MaxiCode Mode 5 | Enhanced ECC` |
| |||||
| **code128** | High-density alphanumeric (Logistics, Tracking) | 300 | 100 | `CODE-128-DEMO` |
| **code128a** | Code 128 Subset A (ASCII control chars) | 300 | 100 | `CODE 128 A` |
| **code128b** | Code 128 Subset B (Standard alphanumeric) | 300 | 100 | `code 128 b` |
| **code128c** | Code 128 Subset C (Double-density numeric) | 300 | 100 | `123456789000` |
| **gs1_128** | GS1-compliant Code 128 (Shipping Containers, SSCC) | 300 | 100 | `(01)12345678901231` |
| **isbn** | International Standard Book Number (Publishing) | 300 | 100 | `978-3-16-148410-0` |
| **code39** | Legacy alphanumeric (Automotive, Defense) | 300 | 100 | `CODE-39` |
| **code93** | High-density alphanumeric (Compact Code 39 alternative) | 300 | 100 | `CODE-93` |
| **codabar** | Legacy numeric (Libraries, Blood Banks) | 300 | 100 | `A12345678B` |
| **ean13** | Global Retail Product ID (GTIN-13) | 300 | 100 | `9780201379624` |
| **ean8** | Compact Retail Product ID (Small packages) | 200 | 100 | `90311017` |
| **upca** | North American Retail Product ID (GTIN-12) | 300 | 100 | `012345678905` |
| **upce** | Zero-suppressed UPC-A (Small retail packages) | 200 | 100 | `01234565` |
| **itf** | Interleaved 2 of 5 (Corrugated boxes, Distribution) | 300 | 100 | `1234567890` |
| **itf14** | GS1 Implementation of ITF (Shipping Containers) | 300 | 100 | `1234567890123` |
| **s10** | UPU S10 (International Mail Tracking) | 300 | 100 | `AA123456785US` |
| **gtin8** | GS1 GTIN-8 (EAN-8 equivalent) | 200 | 100 | `12345670` |
| **gtin12** | GS1 GTIN-12 (UPC-A equivalent) | 300 | 100 | `123456789012` |
| **gtin13** | GS1 GTIN-13 (EAN-13 equivalent) | 300 | 100 | `1234567890128` |
| **gtin14** | GS1 GTIN-14 (ITF-14 / GS1-128 equivalent) | 300 | 100 | `1234567890123` |
| **msi** | Modified Plessey (Retail shelf tags, Inventory) | 300 | 100 | `123456789` |
| **sscc** | Serial Shipping Container Code (Logistics Units) | 300 | 100 | `00340434727193666201` |
| **usps_imb** | USPS Intelligent Mail Barcode (Mail sorting) | 300 | 100 | `420460389200190276542900018858` |
| **upca_composite**| UPC-A with linked 2D Component (Retail + Traceability) | 300 | 150 | `123456789012|CompositeData` |
| **upce_composite**| UPC-E with linked 2D Component | 300 | 150 | `01234565|CompositeData` |

> **Note**: Dimensions (Width/Height) are in pixels as set in the test page defaults.
