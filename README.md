# svg-to-pptx-shapes — Claude Skill

> Convert hand-authored SVG / HTML diagrams into **native, fully editable PowerPoint shapes** — not a flattened image.

---

## What it does

Most ways to get an SVG into PowerPoint destroy editability:

| Method | Editable text? | Recolorable shapes? |
|--------|---------------|---------------------|
| Screenshot / image embed | ✗ | ✗ |
| LibreOffice auto-convert | ✗ (text outlined) | △ |
| **This skill** | ✓ | ✓ |

This skill uses `python-pptx` to reconstruct every SVG element as its matching native PowerPoint object — real text boxes, real auto-shapes, real connectors — so you can still edit labels, recolor boxes, and drag arrows after the conversion.

**Supported SVG elements:**

| SVG element | PowerPoint object |
|-------------|------------------|
| `<rect>` | Rectangle / Rounded Rectangle |
| `<circle>` / `<ellipse>` | Oval |
| `<line>` | Straight Connector (with arrowheads) |
| `<path>` M/L/H/V | Freeform polyline |
| `<path>` C/A (curves, arcs) | Sampled freeform (cylinders, curved edges) |
| `<text>` / `<tspan>` | Text box per visual line |
| `linearGradient` | DrawingML gradient fill |
| `stroke-dasharray` | Dashed stroke |
| Marker arrowheads | Head/tail end arrows |

---

## Install

1. Download **[svg-to-pptx-shapes.skill](https://github.com/YOUR_USERNAME/svg-to-pptx-shapes/raw/main/svg-to-pptx-shapes.skill)** *(or clone this repo)*
2. Open **Claude desktop app → Settings → Capabilities**
3. Click **Install skill** and select the `.skill` file

Once installed, Claude will automatically use this skill whenever you ask to convert an SVG or HTML diagram into an editable PowerPoint file.

---

## Usage

Just describe what you want in Claude — no commands to memorize:

```
"Turn this SVG into an editable PowerPoint"
"Convert my HTML diagram to native PPTX shapes (not a picture)"
"I need this flowchart as editable PowerPoint objects"
"Convert this architecture diagram to a .pptx I can edit"
```

Attach your `.html` or `.svg` file and Claude handles the rest: it reads the source, runs the converter, verifies that zero images were rasterized, does a visual QA pass, and delivers a `.pptx` with every element native and editable.

---

## How it works (for the curious)

The bundled `scripts/svg2pptx.py` (~500 lines) walks the SVG tree and maps each element to a `python-pptx` call:

1. Reads the `viewBox` and fits the diagram onto a 16:10 slide (13.333 × 8.333 in) with margin, preserving aspect ratio.
2. Maps fill, stroke, dash, and gradient attributes to DrawingML properties.
3. Splits `<text>` into one text box per visual line, sized to hug its content and positioned by `text-anchor` (left / center / right).
4. Verifies the output — `pictures = 0` on every slide means nothing was rasterized.

See [`references/mapping.md`](references/mapping.md) for the full element-by-element mapping and notes on extending the converter.

---

## Fonts

Font names are written into the `.pptx` and rendered by **your PowerPoint**, not by the conversion environment. The converter defaults to **Roboto** (body) and **Roboto Mono** (monospace). If those fonts aren't installed, PowerPoint substitutes a default face. Install the fonts for a pixel-perfect match, or ask Claude to embed them (increases file size).

---

## Limits

The converter is built for **hand-authored, structurally clean SVG** — the kind a person or an LLM writes with explicit `rect`, `line`, `path`, and `text`. It is **not** designed for:

- Arbitrary exported / minified SVG with thousands of nodes
- `<use>` / `<symbol>` reuse, clip paths, filters, or masks
- `<image>` (raster embeds)
- Rotation / scale / matrix group transforms
- Relative path commands (`m l h v c a`) or smooth curves (`S T Q`)

If your SVG uses these, Claude will flag it and, where feasible, extend the converter for your case.

---

## Requirements

- [Claude desktop app](https://claude.ai/download) (Cowork mode)
- Python 3.8+ with `python-pptx` and `lxml` (installed automatically by the skill)

---

## Contributing

Found an SVG feature the converter doesn't handle? PRs welcome — the script is intentionally small and readable. Open an issue with a minimal SVG reproducer and the expected PowerPoint output.

---

## License

MIT
