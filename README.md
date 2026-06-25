# 🔍 Metadata Forensics Tool

A browser-based digital forensics tool that extracts hidden metadata from files — the same technique used by real security investigators and penetration testers.

**No installation. No backend. No data ever leaves your device.**

---

## What it does

Every file you create or photograph carries invisible "metadata" baked in by your device or software. This tool exposes it:

| File Type | What it reveals |
| --- | --- |
| 📸 **JPEG / TIFF / PNG** | GPS coordinates, camera make/model, software version, timestamps, EXIF/XMP/IPTC data |
| 📕 **PDF** | Author name, creating application, creation/modification dates, keywords, embedded attachments |
| 📘 **DOCX / DOC (Word)** | Author name, last editor, revision count, track changes, comment authors, editing history |
| 🎵 **MP3 / WAV / MP4 / FLAC** | Artist, encoder software, file owner, duration, sample rates, recording dates, embedded pictures |


---

## Why this matters

This is a real-world OSINT (Open Source Intelligence) and digital forensics technique:

* **GPS in photos** → a journalist publishes a photo with location data embedded → their home address is now public.


* **Author in PDFs** → a company leaks a confidential document → the metadata reveals the employee who wrote it.


* **Editing history in DOCX** → an attacker reviews tracked changes and comments → finds sensitive internal discussions and author identities.


* **Device fingerprinting** → multiple "anonymous" photos are linked back to the same camera/phone.



Real tools like [ExifTool](https://exiftool.org) do this at a deeper level — this project gives you the same concepts in a single HTML file you can understand line by line.

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


4. Your tool is now live at `[https://yourusername.github.io/metadata-forensics-tool](https://yourusername.github.io/metadata-forensics-tool)`


---

## Project structure

```
metadata-forensics-tool/
└── index.html      ← The entire tool. One file.

```

Everything is vanilla JavaScript, HTML, and CSS — no frameworks, no build step, no npm. Complex parser libraries are loaded dynamically via CDN.

---

## How it works (technical breakdown)

### JPEG/TIFF/PNG — ExifReader engine

Uses the `ExifReader` library via CDN to read the EXIF, XMP, and IPTC blocks embedded in image files. It automatically extracts tags and calculates GPS rational coordinates into decimal values.

### PDF — PDF.js Document engine

Loads the official `pdf.js` library via CDN to parse the document catalog, info blocks, and metadata streams. It features a custom fallback parser that scans raw file bytes for standard PDF info dictionary keys (`/Author`, `/Creator`, `/CreationDate`) and XMP packets if the main engine fails.

### DOCX / DOC — OOXML and Legacy CFB parsing

Modern DOCX files are parsed using the `JSZip` CDN library to decompress the archive and read `docProps/core.xml`, `app.xml`, and `custom.xml`. The tool also scans review data for tracked changes and comment authors. Legacy `.doc` files are handled by a custom-built OLE Compound File (CFB) property-set parser reading binary streams.

### Media — Binary Audio/Video tag engine

A custom vanilla JS binary parser reads file `ArrayBuffers` to extract ID3v1/ID3v2 tags from MP3s, RIFF/INFO/BWF chunks from WAVs, QuickTime atoms (`moov`, `trak`, `udta`) from MP4/M4As, and Vorbis comments from FLAC/OGG files.

---

## Limitations

| Limitation | Better tool |
| --- | --- |
| Deep memory analysis of multi-gigabyte files | CLI tools (browser memory limits) |
| Offline execution (requires internet for CDNs) | Standard local npm installations |
| Deep forensic system analysis | [Autopsy](https://www.google.com/search?q=https%3A%2F%2Fwww.autopsy.com), [ExifTool](https://exiftool.org)<br> |

---

## License

MIT — use it, fork it, learn from it.
