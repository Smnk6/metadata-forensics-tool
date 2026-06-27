# 🔍 Metadata Forensics Tool

A browser-based digital forensics tool that extracts hidden metadata from files — the same technique used by real security investigators and penetration testers.

**No installation. No backend. No data ever leaves your device.**

---

## What it does

Every file you create or photograph carries invisible "metadata" baked in by your device or software. This tool exposes it:

| File Type | What it reveals |
|-----------|----------------|
| 📸 **JPEG / TIFF / PNG** | GPS coordinates, camera make/model, software version, timestamps, ISO/aperture, ICC profile, XMP/IPTC fields |
| 📕 **PDF** | Author name, creating application, producer, creation/modification dates, keywords, page count, XMP metadata |
| 📘 **DOCX / DOC (Word)** | Author name, last editor, revision count, Office version, timestamps, tracked changes, comment authors |
| 🎵 **MP3 / M4A / WAV / FLAC / OGG** | Artist, encoder software, file owner, embedded URLs, recording date, duration, sample rate |

---

## Why this matters

This is a real-world OSINT (Open Source Intelligence) and digital forensics technique:

- **GPS in photos** → a journalist publishes a photo with location data embedded → their home address is now public
- **Author in PDFs** → a company leaks a confidential document → the metadata reveals the employee who wrote it
- **Software version in DOCX** → an attacker cross-references the Office version with known CVEs → finds an exploit path
- **Device fingerprinting** → multiple "anonymous" photos are linked back to the same camera/phone

Real tools like [ExifTool](https://exiftool.org) and `python-docx` do this at a deeper level — this project gives you the same concepts in a single HTML file you can understand line by line.

---

## How to run it

**Option 1 — Just open the file**
```
Download index.html → double-click it → opens in your browser
```

**Option 2 — Serve it locally (recommended)**
```bash
# Python 3
python3 -m http.server 8000
# then open http://localhost:8000
```

**Option 3 — Deploy to GitHub Pages (free, shareable)**
1. Fork this repo
2. Go to Settings → Pages
3. Set source to `main` branch, root folder
4. Your tool is now live at `https://yourusername.github.io/metadata-forensics-tool`

---

## Project structure

```
metadata-forensics-tool/
└── index.html      ← The entire tool. One file. ~2,100 lines.
```

Everything is vanilla JavaScript — no frameworks, no build step, no npm.

---

## How it works (technical breakdown)

### JPEG / TIFF / PNG — EXIF, XMP, IPTC extraction
Uses the [ExifReader](https://github.com/mattiasw/ExifReader) library (`exifreader@4.41.0`) to parse all major image metadata blocks: EXIF IFD (camera settings, GPS), XMP (Adobe-style extensible metadata), IPTC (press/editorial tags), and ICC profile data. GPS coordinates are decoded from rational values and rendered as a direct Google Maps link.

### PDF — PDF.js document catalog + binary fallback
Uses [PDF.js](https://mozilla.github.io/pdf.js/) (`pdfjs-dist@6.0.227`) as the primary parser to read the document catalog, info dictionary (`Author`, `Creator`, `Producer`, `CreationDate`, etc.), and XMP metadata streams. A secondary raw binary scanner runs as a fallback, searching the file's byte stream for PDF name-value pairs and extracting any embedded XMP packet using regex against the raw content.

### DOCX — OOXML ZIP parsing
DOCX files are ZIP archives containing XML. The tool uses [JSZip](https://stuk.github.io/jszip/) (`jszip@3.10.1`) to decompress the archive and reads `docProps/core.xml` (author, last editor, revision, timestamps), `docProps/app.xml` (Office version, company, word/page counts), and `docProps/custom.xml` (any user-defined properties). It also scans `word/document.xml` and `word/comments.xml` for tracked changes and comment authors.

### DOC (legacy) — OLE Compound File Binary (CFB) parser
Old `.doc` files use Microsoft's proprietary OLE CFB container format. The tool includes a pure-JavaScript CFB parser that reads the file allocation table, walks the directory entries, and extracts the `SummaryInformation` and `DocumentSummaryInformation` property streams to surface author, title, revision, and editing time metadata.

### Audio — Multi-format tag extraction
Supports five audio containers in the browser, all parsed from raw bytes:
- **MP3** — ID3v2 (v2.2 and v2.3) frame parser + ID3v1 trailer scan
- **M4A / MP4** — ISO Base Media File Format atom walker (`ftyp`, `mvhd`, `ilst` tags)
- **WAV** — RIFF chunk reader (fmt, LIST/INFO, Broadcast Wave Extension)
- **FLAC** — Metadata block parser including Vorbis comment fields
- **OGG** — Ogg page reader targeting the Vorbis comment header

---

## Limitations

| Limitation | Better tool |
|------------|-------------|
| HEIC / AVIF / JXL images | `exiftool filename.heic` |
| XMP metadata in standalone XMP files | `exiftool -xmp filename.xmp` |
| PNG animated (APNG) metadata | `exiftool filename.apng` |
| Deep forensic analysis | [Autopsy](https://www.autopsy.com), [ExifTool](https://exiftool.org) |
| Batch / multi-file processing | `exiftool -r ./folder` |

---

## Skills learnt from this project

- **Digital forensics** — understanding how metadata is embedded in file formats
- **OSINT** — how investigators extract identity data from files
- **JavaScript File APIs** — `FileReader`, `ArrayBuffer`, `Uint8Array`
- **Binary file parsing** — reading raw bytes and interpreting structured data
- **Security awareness** — what data you're leaking when sharing files
- **Frontend development** — building a browser-based security tool in vanilla JavaScript

---

## License

MIT — use it, fork it, learn from it.
