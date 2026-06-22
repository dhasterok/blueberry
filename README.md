# Blüberry

A desktop application for creating, editing, and exporting custom colormaps for use in scientific visualisation. Built with Python and PyQt6.

---

## Features

### Colormap editing

Blüberry supports two colormap styles, switchable via a toolbar toggle:

- **Continuous** — smooth gradient defined by draggable control points. Each point has a position (0–1) and a colour; the gradient is interpolated between them. Points can be added, removed, repositioned by dragging, and evenly spaced with one click.
- **Discrete** — a fixed sequence of colour swatches. Colours can be reordered by drag-and-drop and edited individually.

Both styles support:

| Action | Detail |
|---|---|
| Add point/colour | Toolbar button or Edit menu (`Ctrl+Shift+=`) |
| Remove point/colour | Toolbar button or Edit menu (`Ctrl+Shift+-`) |
| Equal spacing | Distribute control points evenly (`Ctrl+I` for invert) |
| Invert | Reverse the colour order |
| Undo / Redo | Full undo stack (`Ctrl+Z` / `Ctrl+Y`) |

### Colormap sources

The **Colormap Selector** in the toolbar gives access to three libraries:

- **Matplotlib** — all standard Matplotlib colormaps
- **Crameri** — the scientifically derived Crameri colormaps (`cmcrameri`)
- **Custom** — user-saved colormaps loaded from CSV files

Any existing colormap can be loaded as a starting point and then modified.

### Colorblind simulation

A live preview strip below the editor renders the current colormap through six vision-simulation filters simultaneously:

| Filter | Description |
|---|---|
| Grayscale | Luminance-only rendering |
| Deuteranopia | Green-blind simulation |
| Protanopia | Red-blind simulation |
| Tritanopia | Blue-blind simulation |
| Sepia | Warm monotone rendering |
| Negative | Inverted colours |

### Preview panel

An optional preview panel (toolbar **Preview** button, `Ctrl+P`) renders the colormap applied to a sample image. The preview mode can be toggled between:

- **Linear** — horizontal gradient bar
- **Circular** — polar/radial gradient
- **Ternary** — ternary diagram

### Image colour picker

The **Color Picker** tool (`Ctrl+Shift+P` or toolbar dropper button) opens an image panel that lets you build a palette directly from a photograph or other image:

- Load an image from file, drag-and-drop, or paste from the clipboard
- Hover over the image to see a pixel-level magnifier with RGB and hex values
- Click any pixel to pick its colour and inject it into the colormap
- **Auto: Palette** — runs K-means clustering on the image to extract a set of representative colours (2–20 colours, configurable) and loads them as a starting palette

### Colour selector

Individual colours are edited through a dedicated colour picker dialog (`src/ColorSelector.py`) featuring:

- Hue-Saturation triangle for visual colour selection
- Hue slider
- Numeric RGB and hex inputs
- 16-slot colour history (two rows of eight) that persists across opens

### File I/O

- **Load** (`Ctrl+O`) — import custom colormaps from a CSV file (one colormap per row: name followed by hex colour values)
- **Save** (`Ctrl+S`) — write the current colormap back to a CSV file; handles both new entries and updates to existing named colormaps

---

## Usage

### Standalone

```bash
python blueberry.py
```

On close, if the colormap has unsaved changes, you will be prompted to **Save**, **Discard**, or **Cancel**.

### As a plugin

`ColormapEditorDialog` (in `src/ColormapEditor.py`) is a self-contained `QDialog` that can be embedded in any PyQt6 application:

```python
from src.ColormapEditor import ColormapEditorDialog

dialog = ColormapEditorDialog(existing_maps, title="Choose Colormap")
if dialog.exec() == QDialog.DialogCode.Accepted:
    model = dialog.get_colormodel()
    mpl_cmap = model.to_mpl_colormap()  # matplotlib-compatible colormap
```

`existing_maps` is a dict of `{name: [hex_colour, ...]}` representing any custom colormaps that should be available under the **Custom** source.

---

## Project structure

```
blueberry.py               Standalone entry point (BlueberryApp wrapper + main)
src/
  ColormapEditor.py        Core editor dialog, model, and all editor widgets
  ColorSelector.py         Hue-saturation colour picker dialog
  ColorPicker.py           Image colour picker widget
  ColorManager.py          Colour space conversion utilities (RGB, HSV, HSL, CMYK, hex)
  CustomWidgets.py         Reusable Qt widgets
  controls.py              ToggleSwitch and CustomAction toolbar components
  ternary_plot.py          Ternary diagram rendering (Shavin Kaluthantri & Derrick Hasterok)
  config.py                Paths and application-wide defaults
resources/
  icons/                   SVG and PNG icons
  images/                  Sample images for preview
  styles/
    custom_colormaps.csv   Default custom colormap library
```

---

## Dependencies

| Package | Version tested | Purpose |
|---|---|---|
| Python | ≥ 3.11 | |
| PyQt6 | 6.11 | GUI framework |
| matplotlib | 3.11 | Colormap rendering and preview |
| numpy | 2.4 | Numerical operations |
| cmcrameri | 1.9 | Crameri scientific colormaps |
| Pillow | 12.2 | Image loading in colour picker |
| scikit-learn | 1.9 | K-means palette extraction |
| scipy | 1.17 | Spatial operations |

Install dependencies:

```bash
pip install PyQt6 matplotlib numpy cmcrameri pillow scikit-learn scipy
```

---

## Author

Derrick Hasterok  
Ternary plotting module co-authored with Shavin Kaluthantri
