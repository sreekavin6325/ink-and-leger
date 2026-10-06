# Ink & Ledger

Ink & Ledger is a responsive, ledger-inspired expense tracker for recording day-to-day spending, setting monthly and category budgets, reviewing trends, and exporting a monthly summary as a PDF.

The project is intentionally small: the complete interface, styling, and application logic live in a single `index.html` file, with a web app manifest and icon assets alongside it. No framework, package manager, or build step is required.

## Features

- Add expenses with an amount, category, note, and date
- Browse ledger entries month by month
- Delete individual expenses
- Set an overall monthly spending target
- Allocate budgets across eight categories
- Track budget health with progress bars and on-track, near-limit, and over-budget states
- Review spending analytics across all recorded months
- See tracked-month, monthly-average, and highest-month statistics
- Inspect monthly and category totals in an accessible chart and data table
- Export the selected month's target, category breakdown, and transactions to an A4 PDF
- Store data locally in the browser when no host database is available
- Use a host-provided database and download service when the page is embedded in a compatible environment
- Adapt to light or dark system appearance and reduced-motion preferences
- Install with standalone app metadata on supported browsers and devices

## Tech stack

| Layer | Technology |
| --- | --- |
| Markup | Semantic HTML5 |
| Styling | Vanilla CSS, custom properties, responsive media queries |
| Application logic | Vanilla JavaScript (ES5-style browser code) |
| Persistence | `localStorage`, with an optional host-provided database API |
| PDF export | [jsPDF](https://github.com/parallax/jsPDF) 4.2.1, loaded from cdnjs |
| Typography | Fraunces, Manrope, and IBM Plex Mono from Google Fonts |
| App metadata | Web App Manifest and PNG icons |

## Project structure

```text
ink-and-ledger-website/
├── index.html             # UI, styles, state, persistence, analytics, and PDF export
├── manifest.json          # Installable web app metadata
├── favicon-32.png         # Browser favicon
├── apple-touch-icon.png   # Apple home-screen icon
├── icon-192.png           # 192×192 app icon
└── icon-512.png           # 512×512 app icon
```

## Getting started

### Prerequisites

You only need a modern web browser. Python 3 is convenient for serving the files locally, but any static HTTP server will work.

The page loads jsPDF and its fonts from public CDNs, so an internet connection is required for the intended typography and PDF export.

### Run locally

1. Extract the project archive.
2. Open a terminal in the extracted `ink-and-ledger-website` directory.
3. Start a local web server:

   ```bash
   python -m http.server 8000
   ```

   On Windows, if `python` is not available as a command, try:

   ```powershell
   py -m http.server 8000
   ```

4. Visit [http://localhost:8000](http://localhost:8000).

Opening `index.html` directly can display the page, but an HTTP server is recommended so browser storage, the manifest, and install-related behavior run in a normal web origin.

## Scripts and build process

There is no `package.json` and there are no npm scripts. The project does not need dependency installation, compilation, or bundling.

All application behavior is implemented by an immediately invoked script near the end of `index.html`. It handles:

- State and example data
- Expense creation and deletion
- Monthly target and category budget updates
- Local or host-backed persistence
- Ledger and analytics rendering
- Month navigation
- PDF generation and download

The only external runtime script is jsPDF:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/4.2.1/jspdf.umd.min.js"></script>
```

## Using the app

### Record spending

Enter an amount, choose one of the available categories, optionally add a note, select a date, and press **Add entry**. The included categories are Food, Transport, Housing, Utilities, Shopping, Entertainment, Health, and Other.

Example entries appear on first launch so the ledger and analytics views are populated. Adding the first real expense replaces those examples in local-storage mode.

### Set budgets

Use **Edit target** to change the overall monthly target. Select **Edit budgets** to allocate that amount among individual categories. Category indicators change when spending reaches 80% of a budget and again when the budget is reached or exceeded.

### Review analytics

Switch to the **Analytics** tab to see monthly totals, the current target reference line, summary statistics, category composition, and an expandable data table. Chart columns support pointer and keyboard focus interactions.

### Export a report

Select the month you want and choose **Monthly summary PDF**. The generated PDF includes the month's total, target status, category figures, transactions grouped by date, and page numbering where needed.

## Data storage

In a regular browser, data is stored under the `ink-and-ledger-v1` key in `localStorage`. It remains on that browser profile and origin; it is not synced between browsers or devices. Clearing site data also removes the ledger.

The stored shape is:

```json
{
  "expenses": [
    {
      "id": "e...",
      "amount": 850,
      "category": "Food",
      "note": "Groceries",
      "date": "2026-10-05",
      "createdAt": "2026-10-05T12:00:00.000Z"
    }
  ],
  "budgets": {
    "Food": 8000,
    "Transport": 2000
  },
  "monthlyTarget": 40000
}
```

When embedded in an environment that provides `window.claude.use("db")`, the app instead subscribes to an `expenses` collection and a `meta/config` document. A compatible `downloads` service is also used when available; otherwise PDF export falls back to the browser's standard download mechanism.

## Deployment

Upload all six project files together to any static host, such as GitHub Pages, Cloudflare Pages, Netlify, Vercel, or an ordinary web server. Keep the relative paths unchanged because `index.html` references the manifest and icons from the same directory.

No environment variables or server-side routes are required for browser-local mode.

## Customization

The main settings are near the start of the script in `index.html`:

- `CATEGORIES` controls category names and colors.
- `DEFAULT_BUDGETS` defines the initial category allocations.
- `DEFAULT_TARGET` sets the initial monthly target.
- CSS custom properties under `:root` control the theme and status colors.
- `fmt()` and `fmtPdf()` control currency display. The current interface uses Indian rupees and the `en-IN` number format.

After changing the local-storage data structure, update `LOCAL_KEY` so existing browser data does not conflict with the new schema.

## Current limitations

- The manifest provides install metadata, but the project does not include a service worker, so it does not cache assets for offline use.
- Browser-local data has no built-in import, backup, account sync, or cross-device synchronization.
- jsPDF and Google Fonts are loaded remotely.
- The optional database and download integrations depend on APIs supplied by the embedding host and are not configured as standalone backend services in this repository.

## License

No license file is included. Add a license before redistributing or accepting external contributions.
