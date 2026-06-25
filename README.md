# 🔍 Metadata Forensics Tool

A browser-based digital forensics tool that extracts hidden metadata from files — the same technique used by real security investigators and penetration testers.

**No installation. No backend. No data ever leaves your device.**

---

## What it does

Every file you create or photograph carries invisible "metadata" baked in by your device or software. This tool exposes it:

| File Type | What it reveals |
|-----------|----------------|
| 📸 **JPEG / TIFF** | GPS coordinates, camera make/model, software version, timestamps, ISO/aperture |
| 📕 **PDF** | Author name, creating application, creation/modification dates, keywords |
| 📘 **DOCX (Word)** | Author name, last editor, revision count, Office version, timestamps |
| 🎵 **MP3 / WAV** | Artist, encoder software, file owner, embedded URLs |

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
└── index.html      ← The entire tool. One file. ~400 lines.
```

Everything is vanilla JavaScript — no frameworks, no build step, no npm.

---

## How it works (technical breakdown)

### JPEG/TIFF — EXIF extraction
Uses the [exif-js](https://github.com/exif-js/exif-js) library to read the EXIF IFD (Image File Directory) embedded in JPEG files. EXIF data is stored in a standardized binary format (TIFF structure) in the image header.

### PDF — Binary header parsing
Reads the raw file bytes and searches for standard PDF info dictionary keys (`/Author`, `/Creator`, `/Producer`, `/CreationDate`, etc.) using regular expressions on the binary stream.

### DOCX — OOXML ZIP parsing
DOCX files are ZIP archives containing XML files. The tool scans the raw bytes for `docProps/core.xml` content, which stores `<dc:creator>`, `<cp:lastModifiedBy>`, and revision metadata as plain XML tags.

### Audio — ID3 tag extraction
MP3 files begin with an `ID3` header containing tag frames (TIT2 = title, TPE1 = artist, TENC = encoder, etc.). The tool reads the first 6KB of the file and extracts printable ASCII content from known frame offsets.

---

## Limitations

| Limitation | Better tool |
|------------|-------------|
| PNG text chunks | `exiftool filename.png` |
| Old .doc binary format | `python-docx` or LibreOffice |
| XMP metadata in PDFs | `exiftool -xmp filename.pdf` |
| Deep forensic analysis | [Autopsy](https://www.autopsy.com), [ExifTool](https://exiftool.org) |

---

## Resources to go deeper

- [ExifTool documentation](https://exiftool.org) — the gold standard CLI tool
- [EXIF spec](https://www.exif.org/Exif2-2.PDF) — the actual standard
- [OOXML file format](https://docs.microsoft.com/en-us/openspecs/office_file_formats/ms-docx) — how Word documents are structured
- [ID3 tag spec](https://id3.org/id3v2.3.0) — MP3 metadata standard
- [OWASP Metadata Leakage](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/02-Fingerprint_Web_Server) — how this is used in real pentests

---

## License

MIT — use it, fork it, learn from it.
