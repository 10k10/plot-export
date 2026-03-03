# PlotDigitizer V2 — Auto-Trace Curve Extraction

A zero-dependency, single-file browser tool for extracting numerical data from plot images. Open `PlotExport.html` directly in any modern browser — no installation, no server required.

**Author:** ninajpones *(code written with AI assistance)*

---

## Features

- **Three operating modes:** Calibrate, Digitize (manual click), and Auto-Trace
- **Axis calibration** with support for both linear and logarithmic scales (X and Y independently)
- **Auto-Trace** color-based curve detection — draw roughly over the curve, let the algorithm find it
- **Multiple datasets** — up to 8 color-coded series per session
- **Pixel magnifier** with crosshair and live color readout (4x to 16x zoom)
- **Live coordinate display** — cursor position shown in real-world units as you move
- **Session save/load** — preserve calibration and digitized points as JSON
- **Export** to CSV, tab-delimited TXT, or directly to clipboard
- **Keyboard shortcuts** for common actions
- **Drag & drop** image loading

---

## Quick Start (Auto-Trace)

1. Open `PlotExport.html` in your browser
2. **Open image** — click `📂 Open` or drag & drop a PNG/JPG/BMP/WebP onto the canvas
3. **Calibrate X axis**
   - Click `Pick X₁`, then click on a known X position in the image
   - Enter its real value in the text field
   - Repeat for `Pick X₂`
   - Click `✓ Apply X`
   - Check **Logarithmic** if the axis is log-scale
4. **Calibrate Y axis** — same process with `Pick Y₁`, `Pick Y₂`, `✓ Apply Y`
5. **Switch to Auto-Trace mode** (`🖊 Auto-Trace` button)
6. **Sample the curve color** — click `Pick from Image`, then click directly on the curve line
7. **Draw a path** — hold the left mouse button and drag roughly along the curve
8. **Trace** — click `⚡ Trace!` — the algorithm detects the curve within the drawn corridor
9. **Accept** — click `✓ Accept` to commit the points to the active dataset
10. **Export** — click `💾 CSV` or `📋 Copy`

---

## Manual Digitize Mode

Switch to `✚ Digitize` mode and click anywhere on the plot to place data points manually.

- **Right-click** a point to delete it
- **Ctrl+Z** to undo the last placed point
- Points are shown as colored dots with connecting dashed lines

---

## Auto-Trace — How It Works

The tracing algorithm operates on a **corridor** defined by the drawn brush path:

1. A mask is built from the brush stroke at the configured pen width
2. For each pixel column (X) within the corridor, all pixels matching the target color within the tolerance threshold are found
3. Their Y positions are averaged using **inverse-distance-weighted centroids** (closer color match = higher weight)
4. The resulting raw points are **smoothed** with a sliding window average
5. If more than 300 points are detected, the result is **subsampled** to ~200 evenly spaced points

### Tuning parameters

| Parameter | Default | Effect |
|---|---|---|
| Pen Width | 30 px | Corridor width. Wider = more forgiving, but may capture nearby gridlines |
| Color Tolerance | 35 | Euclidean RGB distance threshold. Raise if curve is missed, lower to reduce noise |
| Smoothing | 5 | Sliding window size. Higher = smoother curve, less sensitive to pixel noise |

**Tips:**
- Pick color by clicking directly **on the curve line**, not the background
- For log-log plots (e.g. SOA diagrams): gridlines are usually gray, curve is colored — good contrast
- For multiple curves: add a new **Dataset** per curve, pick each curve's color separately

---

## Navigation

| Action | Control |
|---|---|
| Zoom in/out | Scroll wheel |
| Pan | Middle-click drag |
| Fit image to window | `Fit` button |
| Zoom step buttons | `🔍+` / `🔍−` |

---

## Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl+O` | Open image |
| `Ctrl+S` | Export CSV |
| `Ctrl+Z` | Undo last digitized point |
| `Delete` | Delete selected table row |
| `Escape` | Cancel active pick / exit color-pick mode |

---

## Datasets

Multiple datasets can be digitized in a single session — useful for plots with several curves.

- Click `+` to add a new dataset
- Click `Rename` to give it a meaningful name
- Switch the active dataset from the dropdown
- Each dataset has its own color (8 colors cycle automatically)
- All datasets are included in CSV/TXT exports, separated by comment headers

---

## Session Save & Load

Click `Save Session` to download a `.json` file containing:
- Full calibration state (both axes, linear/log flags)
- All datasets and their points
- The active trace color

Click `Load Session` to restore a saved session. Note: the image itself is not stored — you need to reopen it manually after loading.

---

## Export Formats

| Format | Description |
|---|---|
| **CSV** | Comma-separated, one section per dataset with `# Dataset Name` header |
| **TXT** | Tab-separated, same structure as CSV |
| **Clipboard** | Same as CSV, copied to clipboard — useful when `file://` download is blocked |

---

## File Format Support

Images: **PNG, JPG, BMP, WebP** — loaded via the browser's native `Image` API, so any format your browser supports will work.

---

## Technical Notes

- **Zero dependencies** — pure HTML5 + vanilla JavaScript + CSS, single file
- **No server needed** — open directly from disk (`file://`)
- Pixel data is accessed via `OffscreenCanvas` / `ImageData` for color matching
- Calibration math supports both linear interpolation and log₁₀ interpolation
- The download function includes three fallback strategies for environments where `Blob` URLs are restricted (e.g. strict `file://` contexts)

---

## Step-by-Step Reference

A detailed illustrated walkthrough is available in `PlotDigitizer_V2_README_StepByStep.pdf` included in this repository.

---

## License

Free to use and modify. If you redistribute or build on this tool, a credit to the original author is appreciated.

> *PlotDigitizer V2 — by ninajpones. Code written with AI assistance.*
