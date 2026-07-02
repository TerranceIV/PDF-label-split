# PDF Label Split

A lightweight, browser-only tool that splits multi-page PDFs into individual page files and stamps each page with a matched reference number — no server, no upload, fully offline-capable.

> All sample files in this repository (PDFs, Excel sheets) are for demo purposes only.

## Overview

PDF Label Split is a single self-contained HTML application designed for splitting batch shipping label PDFs into one file per page, while optionally stamping each page with a corresponding order/reference number pulled from an Excel mapping file. Everything runs client-side in the browser using embedded `pdf.js`, `pdf-lib`, and `SheetJS (xlsx)` — no internet connection or backend server is required after the file is loaded.

## Features

- **Split PDF into individual pages** — one output file per page from the uploaded PDF.
- **Automatic page naming** — extracts a matching code (default pattern: `SPXSG` followed by digits) from each page's text and uses it as the output filename.
- **Configurable regex pattern** — a plain-text input lets you change the matching keyword (e.g. `SPXSG`) without writing regex; a **Verify** button re-runs extraction on demand and refreshes the log.
- **Excel mapping upload** — upload a `.xlsx`/`.xls` file with two columns, `No.` and `External Document No.`, to build a lookup table.
- **Label stamping** — matches each page's extracted code against the Excel mapping and stamps the corresponding `No.` onto the page using Helvetica, centered horizontally, positioned near the bottom.
- **Two processing modes**:
  - **Mode 1: Split into pages and stamp label for each** — splits the PDF and stamps every resulting page.
  - **Mode 2: Stamp label only for each page (no splitting)** — stamps the original multi-page PDF in place without splitting it.
- **Toast notification** — a top-center toast confirms completion, indicating which mode just finished.
- **Folder or downloads output** — choose to save directly into a selected folder (Chrome/Edge, via File System Access API) or fall back to standard downloads.
- **Fully offline** — `pdf.js`, `pdf-lib`, and `SheetJS` are embedded directly in the HTML file as base64, so the app works without any CDN or network access.

## How It Works

1. **Upload a PDF** — click or drag-and-drop a multi-page PDF into the drop zone.
2. **(Optional) Set the regex pattern** — the default is `SPXSG`; change it if your documents use a different keyword, then click **Verify** to preview matches in the log.
3. **(Optional) Upload an Excel mapping file** — a spreadsheet with `No.` and `External Document No.` columns. Matching is case-insensitive and whitespace-trimmed.
4. **Choose a mode**:
   - Mode 1 — split and stamp every page.
   - Mode 2 — stamp only, keep as one file.
5. **Click "Split into pages"** — the app reads each page's text, matches the pattern, looks up the mapping, stamps the result, and either splits or keeps the document based on the selected mode.
6. **Save the output** — pick a folder to save directly into, or let files download individually.

## Matching Logic

- Each page's text is extracted using `pdf.js`.
- The app searches for the configured pattern (default: `\bSPXSG\d+\b`, case-insensitive).
- If a match is found, it's looked up in the Excel mapping table (`External Document No.` → `No.`).
- If a corresponding `No.` exists, it is stamped onto that page.
- If no match is found in the PDF text or the Excel mapping, the page is left unchanged — no error, no blocking.
- If the same code appears on multiple pages, all matching pages receive the same stamped value.

## Configuration

A small config block near the top of the script allows adjusting behavior without touching the core logic:

```js
// [USER CONFIG]
var headerList = ["no.", "no", "external document no.", "external document no"];
var stampedPos = { x: 0, y: 20 };
var stampFontSize = 14;
```

- `headerList` — accepted column header variants for the Excel mapping (index 0–1 for `No.`, index 2–3 for `External Document No.`).
- `stampedPos` — horizontal offset (`x`) and vertical position from the bottom (`y`) of the stamped text.
- `stampFontSize` — font size (in points) used for the stamped label text.

## Browser Support

- Works in any modern browser.
- "Save into a folder I choose" requires the File System Access API (Chrome / Edge). Other browsers automatically fall back to standard file downloads.

## Sample Files

This repository includes sample PDFs and Excel files for demonstration and local testing only. They do not represent real shipment or customer data.

## Privacy

No file is uploaded to any server. All PDF parsing, Excel parsing, and stamping happen entirely in your browser.
