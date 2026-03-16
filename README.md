# VH(bb̄) Postfit Distribution Viewer

An interactive browser-based tool for visualizing postfit mass distributions from the ATLAS boosted VH(bb̄) analysis. Built with React and HTML5 Canvas — no ROOT required.

![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-5-646CFF?logo=vite&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)

---

## Features

- **Multi-region overlay** — Compare signal regions, control regions, pT bins, and b-tag categories on the same canvas
- **Per-process toggling** — Show/hide individual physics processes (VH, top, V+jets, multijets, VV, Data, Total MC, etc.)
- **Ratio panel** — Bottom panel showing bin-by-bin ratio to any selected reference distribution
- **Dynamic ratio denominator** — Dropdown updates to reflect only the currently visible curves
- **Normalize to area** — Shape comparison mode with properly propagated Poisson errors
- **Log scale** — Toggle logarithmic Y axis
- **Uncertainty bands** — Postfit uncertainty visualization on both the main and ratio panels
- **Folder upload** — Select a directory and automatically scan for `*_postfit.yaml` files
- **Drag & drop** — Drop folders directly onto the app; recursively scans for postfit files
- **Blinded bins** — Gracefully handled as gaps in the distributions
- **HiDPI / Retina** — Canvas renders at native device pixel ratio

## Quick Start

### Prerequisites

You need [Node.js](https://nodejs.org/) (v16+). Check with:

```bash
node -v
npm -v
```

If not installed, get it via Homebrew (macOS), your package manager (Linux), or the [Node.js website](https://nodejs.org/):

```bash
# macOS
brew install node

# Ubuntu/Debian
sudo apt install nodejs npm
```

### Setup

1. **Clone the repo**

```bash
git clone https://github.com/<your-username>/vh-postfit-viewer.git
cd vh-postfit-viewer
```

2. **Create the React app** (first time only)

```bash
npm create vite@latest . -- --template react
```

If it warns about existing files, that's fine — it won't overwrite `final_app.jsx`.

3. **Install dependencies**

```bash
npm install
```

4. **Wire up the app entry point**

Replace the contents of `src/App.jsx` with an import from the viewer:

```bash
cp final_app.jsx src/App.jsx
```

5. **Run the dev server**

```bash
npm run dev
```

Open the URL shown in your terminal (typically `http://localhost:5173/`).

That's it — the viewer is running locally in your browser.

### One-liner (after cloning)

```bash
npm create vite@latest . -- --template react && npm install && cp final_app.jsx src/App.jsx && npm run dev
```

## Usage

### Loading Data

The app comes pre-loaded with example Run-2 postfit distributions. To load your own:

1. Click **"📁 Select postfit folder"** in the sidebar
2. Choose the directory containing your YAML files
3. The app scans for all files matching `*_postfit.yaml` and loads them automatically

You can also **drag and drop** a folder directly onto the app window. Subdirectories are scanned recursively.

> If no `*_postfit.yaml` files are found, an error message is displayed.

### Input Format

The tool expects YAML files in the standard postfit output format:

```yaml
Samples:
  - Name: VH
    Yield: [0.24, 0.30, 0.37, ...]
  - Name: top
    Yield: [224.0, 238.3, ...]
  # ... more processes
Total:
  - Yield: [17377.6, 16232.2, ...]
    UncertaintyUp: [131.5, 127.2, ...]
    UncertaintyDown: [-131.5, -127.2, ...]
Data:
  - Yield: [17382, 16235, ..., blinded, blinded, ..., 7546, ...]
Figure:
  - BinEdges: [60, 65, 70, ..., 200]
    XaxisLabel: Higgs Candidate Jet Mass [GeV]
```

Region metadata (SR/CR, pT bin, b-tag category) is parsed from the filename:

| Filename pattern | Parsed as |
|---|---|
| `bin250_450_doubleBtagged_Run2_postfit.yaml` | SR, pT 250–450, 2×b-tag |
| `bin750_antiBtagged_Run2_postfit.yaml` | SR, pT 750–∞, anti-b-tag |
| `CR_H_bin250_450_antiBtagged_Run2_postfit.yaml` | CR_H, pT 250–450, anti-b-tag |

### Controls

| Control | Description |
|---|---|
| **Regions** | Toggle individual regions on/off. Quick buttons: All, None, 2b only |
| **Processes** | Toggle physics processes. Quick buttons: All, Data+MC, Signal, None |
| **Log Y** | Logarithmic Y axis |
| **Uncertainty bands** | Show/hide postfit uncertainty bands |
| **Normalize to area** | Shape-only comparison (each distribution scaled to unit area) |
| **Ratio panel** | Toggle bottom ratio panel |
| **Ratio denominator** | Choose which visible curve serves as the reference for ratios |

### Multi-Region Mode

When multiple regions are selected, each region gets a unique color and dash pattern. Legend entries show the full label (e.g., "SR pT 250–450 doubleBtagged / Total MC"). The ratio denominator dropdown lists all visible curves so you can ratio across regions.

## Project Structure

```
vh-postfit-viewer/
├── final_app.jsx      # Self-contained viewer (preloaded data + full UI)
├── README.md
└── src/
    └── App.jsx        # ← copy of final_app.jsx (after setup)
```

The entire application lives in a single `final_app.jsx` file — including the YAML parser, canvas renderer, sidebar UI, and pre-loaded example data. No external plotting libraries needed.

## Technical Notes

- **Rendering**: Pure HTML5 Canvas (no D3/Chart.js/Plotly). Step histograms, error bars, uncertainty bands, and the ratio panel are all drawn programmatically.
- **Poisson errors**: Data points show √N error bars. When normalized to area, errors are correctly computed as √N / area (not √(N/area)).
- **Blinded bins**: Bins with `blinded` yield values are rendered as gaps. No data points or ratio entries are drawn for blinded bins.
- **YAML parsing**: Lightweight regex-based parser — no `js-yaml` dependency needed. Handles the specific postfit YAML schema.

## License

MIT
