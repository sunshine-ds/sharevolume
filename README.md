# SEC Shares Outstanding Viewer (Aon plc default)

## Overview

This is a single-file, responsive web application that loads the SEC XBRL concept EntityCommonStockSharesOutstanding for a company CIK (Aon plc by default: CIK 0000315293). It extracts shares outstanding data for fiscal years after 2020, finds the maximum and minimum reported values, and produces a `data.json` file matching the requested structure.

The app is built with Tailwind CSS (via CDN) and all JavaScript is inline in `index.html` so it can be hosted as a static page.

## Features

- Fetches SEC XBRL concept JSON for EntityCommonStockSharesOutstanding via the SEC data API (using a public CORS proxy).
- Default company: Aon plc (CIK 0000315293).
- If opened with `index.html?CIK=0001018724` (or any 10-digit CIK), the app will fetch and display that CIK's data without reloading the page.
- Filters `.units.shares[]` entries to include only those where the fiscal year (fy) is greater than 2020 and the `val` is numeric.
- Computes the maximum and minimum `.val` among the filtered entries and displays them prominently.
- Saves a `data.json` file with the structure:
  {
    "entityName": "Aon plc",
    "max": { "val": ..., "fy": ... },
    "min": { "val": ..., "fy": ... }
  }
  The app will automatically trigger a download of `data.json` after a successful fetch, and you can manually download the last result using the "Download data.json" button.
- All required elements include IDs:
  - `share-entity-name`
  - `share-max-value`
  - `share-max-fy`
  - `share-min-value`
  - `share-min-fy`
  The `<title>` and `<h1>` are updated to include the live `entityName`.
- Attempts to read `uid.txt` from the project root (if served) and displays it in the header.

## Usage

1. Host the files (static host, e.g., GitHub Pages, Netlify, or local static server). Ensure `uid.txt` (if present) is placed in the project root so the app can fetch and show it.

2. Open `index.html` in a modern browser. By default, the app will fetch the Aon plc data (CIK 0000315293), display the entity name, show the maximum and minimum shares outstanding entries (for years after 2020), and automatically download `data.json`.

3. To view another company, either:
   - Add `?CIK=0001018724` (CIK must be digits; it will be normalized to 10 digits) to the URL, e.g. `index.html?CIK=0001018724`, or
   - Enter a CIK in the input at the top and click "Fetch".

   The app will fetch through a CORS proxy and update the page content (title and H1) without reloading.

4. Use the "Download data.json" button to manually download the last computed JSON again.

## Notes & Limitations

- Browser security prevents setting the actual `User-Agent` header from client-side JavaScript. Per the SEC guidance, a descriptive agent should be sent, but browsers restrict modification of the `User-Agent`. This app sets `X-User-Agent` and `From` headers when possible and uses a public CORS proxy to fetch SEC data.

- The app attempts to be tolerant of variations in the SEC JSON structure. It expects `entityName` and an array under `units` that corresponds to shares; some filings can use different unit keys. If the expected structure is missing, the app will show a helpful error.

- The filtering of `fy` is robust: it extracts a 4-digit year from various formats and compares numerically (> 2020).

- The app will attempt to download `data.json` automatically after a successful fetch. If your browser blocks automatic downloads, you can use the "Download data.json" button.

## Files

- `index.html` — Main single-file web app. Includes Tailwind CSS via CDN and all inline JavaScript.
- `README.md` — This documentation.
- `LICENSE` — MIT License.
- `uid.txt` — (Optional) If present at project root on the same host, it will be read and displayed in the UI.

## License

MIT License — see LICENSE file.
