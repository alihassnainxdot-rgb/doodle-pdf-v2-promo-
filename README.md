# DoodlePDF Backend - Python Flask PDF Processing

## Overview

DoodlePDF is a comprehensive PDF processing suite with 31+ tools, advanced OCR with crop regions, and server-side Python backend for true PDF compression and large file support.

## Features

### PDF Operations
- **Merge** - Combine multiple PDFs
- **Split** - Split by page ranges or extract pages
- **Compress** - True Ghostscript-level compression with quality levels
- **Rotate** - Rotate specific pages or all pages
- **Crop** - Crop margins or custom areas per page
- **Page Numbers** - Add with custom format and position
- **Reorder/Delete** - Organize pages

### OCR & AI
- **Advanced OCR** with:
  - Page range selection (e.g., pages 3-7 only)
  - Custom crop regions (normalized 0-1 coordinates)
  - Multi-language support (English, Spanish, French, German, Chinese, Japanese, Korean)
  - Configurable DPI (150/300/600)
  - Multiple output formats (searchable PDF, TXT, JSON)
  - Force OCR option for scanned documents
- **AI Summarizer** - Extractive summarization
- **Compare PDFs** - Visual diff with red highlighting

### Conversion
- **PDF to Word** - DOCX with table preservation
- **PDF to Excel** - XLSX with table extraction
- **PDF to Images** - JPG/PNG with DPI control
- **PDF to HTML/Markdown** - Styled output
- **Images to PDF** - Compile with page size options

### Security
- **Protect** - AES-256 encryption with permissions
- **Unlock** - Remove password
- **Repair** - Fix corrupted PDFs
- **PDF/A** - Archival conversion

### Editing
- **Watermark** - Text with opacity, angle, color
- **Sign** - Add signature images
- **Redact** - Permanently blackout areas
- **Forms** - Fill AcroForm fields

## Quick Start

### Option 1: Docker (Recommended)

```bash
docker-compose up --build
```

Open http://localhost:5000

### Option 2: Local Python

1. **Install system dependencies:**

```bash
# Ubuntu/Debian (wkhtmltopdf is optional; PyMuPDF renders HTML as a fallback)
sudo apt-get install tesseract-ocr tesseract-ocr-eng poppler-utils libreoffice ghostscript

# macOS
brew install tesseract tesseract-lang poppler libreoffice ghostscript
```

> **OCR languages:** only English (`eng`) is installed by default. Add more with
> e.g. `sudo apt-get install tesseract-ocr-spa tesseract-ocr-fra`. If your
> `.traineddata` files live somewhere non-standard, set `TESSDATA_PREFIX`.

2. **Install Python packages:**

```bash
cd backend
pip install -r requirements.txt
```

3. **Run the server:**

```bash
cd backend
python run.py
```

Open http://localhost:5000

## API Endpoints

### OCR with Page Ranges and Crop Regions

**POST /api/ocr/pdf**

| Parameter | Type | Description |
|-----------|------|-------------|
| file | File | PDF file |
| page_ranges | String | "1-3,5,7-9" or "all" |
| regions | JSON | `[{"page_num":1,"x":0.1,"y":0.1,"width":0.8,"height":0.3}]` |
| languages | String | "eng", "eng+spa", "chi_sim", etc. |
| dpi | Integer | 150, 300, or 600 |
| force_ocr | Boolean | Force OCR even with digital text |
| output_format | String | "pdf" (searchable), "txt", or "json" |

**Example with curl:**

```bash
# Basic OCR
curl -X POST http://localhost:5000/api/ocr/pdf \
  -F "file=@document.pdf" \
  -F "page_ranges=1-5,10" \
  -F "languages=eng+spa" \
  -F "dpi=300" \
  -F "output_format=pdf" \
  -o searchable.pdf

# OCR with crop regions (first page, top 30% area)
curl -X POST http://localhost:5000/api/ocr/pdf \
  -F "file=@document.pdf" \
  -F 'regions=[{"page_num":1,"x":0.0,"y":0.0,"width":1.0,"height":0.3}]' \
  -F "languages=eng" \
  -o ocr_result.json
```

### Compress PDF

**POST /api/pdf/compress**

```bash
curl -X POST http://localhost:5000/api/pdf/compress \
  -F "file=@large.pdf" \
  -F "quality=extreme" \
  -o compressed.pdf
```

### Merge PDFs

**POST /api/pdf/merge**

```bash
curl -X POST http://localhost:5000/api/pdf/merge \
  -F "files=@doc1.pdf" \
  -F "files=@doc2.pdf" \
  -F "files=@doc3.pdf" \
  -o merged.pdf
```

### All Endpoints

| Method | Endpoint | Purpose |
|--------|----------|---------|
| POST | `/api/pdf/merge` | Merge multiple PDFs |
| POST | `/api/pdf/split` | Split by page ranges |
| POST | `/api/pdf/compress` | Optimise / compress |
| POST | `/api/pdf/info` | Page + metadata info |
| POST | `/api/pdf/page-numbers` | Add page numbers |
| POST | `/api/organize/rotate` | Rotate pages |
| POST | `/api/organize/reorder` | Reorder pages |
| POST | `/api/organize/delete` | Delete pages |
| POST | `/api/organize/extract` | Extract pages |
| POST | `/api/organize/crop` | Crop pages |
| POST | `/api/edit/watermark` | Text watermark |
| POST | `/api/edit/redact` | Permanently redact areas |
| POST | `/api/edit/sign` | Add signature image |
| POST | `/api/edit/forms/fields` | List AcroForm fields |
| POST | `/api/edit/forms/fill` | Fill AcroForm fields |
| POST | `/api/edit/page-numbers` | Add page numbers |
| POST | `/api/security/protect` | Encrypt with password |
| POST | `/api/security/unlock` | Remove password |
| POST | `/api/security/repair` | Repair corrupted PDF |
| POST | `/api/security/to-pdfa` | Convert to PDF/A |
| POST | `/api/convert/to-word` | PDF → DOCX |
| POST | `/api/convert/to-excel` | PDF → XLSX |
| POST | `/api/convert/to-images` | PDF → JPG/PNG (zip) |
| POST | `/api/convert/to-html` | PDF → HTML |
| POST | `/api/convert/to-markdown` | PDF → Markdown |
| POST | `/api/convert/images-to-pdf` | Images → PDF |
| POST | `/api/convert/html-to-pdf` | HTML → PDF |
| POST | `/api/convert/word-to-pdf` | DOCX/DOC → PDF (LibreOffice) |
| POST | `/api/convert/excel-to-pdf` | XLSX/XLS → PDF (LibreOffice) |
| POST | `/api/ocr/pdf` | OCR with page ranges + regions |
| POST | `/api/ocr/image` | OCR an image |
| POST | `/api/ocr/preview-regions` | Render region previews |
| GET  | `/api/ocr/languages` | List installed OCR languages |
| POST | `/api/ai/summarize` | Extractive summary |
| POST | `/api/ai/extract-text` | Extract clean text |
| POST | `/api/ai/translate` | Translate extracted text |
| POST | `/api/ai/compare` | Visual diff of two PDFs |
| POST | `/api/ai/smart-split` | Detect logical sections |
| GET  | `/api/health` | Health check |

## Legacy Client-Only App

The original browser-only version (pdf-lib + Tesseract.js) is preserved under
[`legacy/`](legacy/README.md) for reference. It is no longer used by the server.

## Project Structure

```
doodle-pdf/
├── backend/
│   ├── app/
│   │   ├── __init__.py          # Flask app factory
│   │   └── api/
│   │       ├── pdf.py           # PDF operations (merge/split/compress/info)
│   │       ├── ocr.py           # OCR with regions
│   │       ├── convert.py       # Format conversions
│   │       ├── organize.py      # Page organization
│   │       ├── security.py      # Encrypt/unlock/repair/PDF-A
│   │       ├── edit.py          # Watermark/sign/redact/forms
│   │       └── ai.py            # Summarize/translate/compare
│   ├── services/
│   │   └── pdf_service.py       # Core processing service
│   ├── requirements.txt
│   └── run.py                   # Entry point
├── frontend/
│   ├── static/
│   │   ├── app.js               # Frontend application (ES module)
│   │   └── styles.css           # Hand-drawn theme
│   └── templates/
│       └── index.html           # Main UI (served by Flask)
├── legacy/                      # Original browser-only app (archived)
├── Dockerfile
└── docker-compose.yml
```

The app is served entirely by Flask: `/` renders the UI and `/api/*` serves the
JSON/binary endpoints. `frontend/static/*` is served at `/static/*`.

## Key Features Explained

### Advanced OCR with Crop Regions

The OCR system supports **custom crop regions** defined by normalized coordinates (0-1 relative to page size). This allows you to OCR specific areas of a page:

```json
[
  {
    "page_num": 1,
    "x": 0.1,
    "y": 0.05,
    "width": 0.8,
    "height": 0.3
  }
]
```

This would OCR a region that starts at 10% from left, 5% from top, spanning 80% of page width and 30% of page height.

### True PDF Compression

Unlike client-side rasterization (which converts everything to images), the backend uses:

- **pikepdf** for object stream optimization
- **Ghostscript** for advanced compression
- **Image downsampling** with configurable DPI

### Large File Support

The server-side architecture supports files up to 500MB with:
- Background processing
- Configurable timeouts
- Memory-efficient streaming

## Development

### Adding New Tools

1. Add service method in `backend/services/pdf_service.py`
2. Add API endpoint in `backend/app/api/`
3. Add tool definition in `frontend/static/app.js`

### Running Tests

```bash
cd backend
python -m pytest tests/
```

## System Requirements

- **Python 3.10+**
- **Tesseract OCR 5.0+**
- **4GB RAM minimum** (8GB recommended for large PDFs)
- **Docker** (recommended) or system dependencies

## License

MIT