# XU.SVG.Progress

![Screenshot.png](https://private-user-images.githubusercontent.com/173887760/499646812-a47f802c-78fa-46e4-9446-dc9a386d8d6f.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NjAwNjMxMjEsIm5iZiI6MTc2MDA2MjgyMSwicGF0aCI6Ii8xNzM4ODc3NjAvNDk5NjQ2ODEyLWE0N2Y4MDJjLTc4ZmEtNDZlNC05NDQ2LWRjOWEzODZkOGQ2Zi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjUxMDEwJTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI1MTAxMFQwMjIwMjFaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT04Y2Q5MmQ2YmZlZTBhOTVkMTRhZTRhY2RmMzFiZjI1Y2FkMjJmODA3NmZmY2ViY2E1ZWY4NmE5YjY3ZjYzNDJkJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.fS2IlW48cl1KDWa5lkoA163vtji8S0b_Dx4WtQ3hVUs)

A DAX user-defined function (UDF) library for generating **SVG-based progress indicators** directly inside Power BI — no custom visuals required.

Supports multiple styles: horizontal bars (simple, capsule, dashed, labeled), circular pie, and donut charts — all fully customizable via parameters or shared configuration.

---

## What it does

The `XU.SVG.Progress` package provides six ready-to-use functions to render progress visuals as **SVG images** in Power BI:

- **Bar-style**: `SimpleBar`, `Capsule`, `Dashed`, `LabeledBar`
- **Circular-style**: `Pie`, `Donut`

All functions return a **data URL string** (e.g., `data:image/svg+xml;utf8,<svg>...</svg>`) that can be displayed as an image when the measure’s **Data Category** is set to **ImageUrl**.

These functions eliminate the need for external visuals or complex conditional formatting, enabling native, model-level KPI rendering.

---

## Functions

### `XU.SVG.Progress.SimpleBar (ProgressRatio, BarBackgroundColor, BarFillColor, BarCornerRadius, IfShowLabel, LabelContent, LabelTextColor)`

Renders a basic horizontal progress bar with optional right-aligned label.

- **Use case**: Standard progress indicators in tables or cards.
- **Customizable**: Background/fill color, corner radius, label text/color.

---

### `XU.SVG.Progress.Capsule (ProgressRatio, BarBackgroundColor, BarFillColor, IfShowLabel, LabelContent, LabelTextColor)`

Renders a rounded “capsule”-style progress bar (inherits from `SimpleBar` with max corner radius).

- **Use case**: Modern, visually distinct progress bars.
- **Note**: No `BarCornerRadius` parameter — always fully rounded.

---

### `XU.SVG.Progress.Dashed (ProgressRatio, LineColor, IfShowLabel, LabelContent, LabelTextColor)`

Renders a dashed-line progress indicator (background = semi-transparent, fill = solid).

- **Use case**: Lightweight or minimalist dashboards.

---

### `XU.SVG.Progress.LabeledBar (ProgressRatio, BarBackgroundColor, BarFillColor, BarCornerRadius, IfShowLabel, LabelContent, LabelTextColor)`

Renders a **two-line layout**: large label **above** a mini progress bar.

- **Use case**: KPI summaries where label clarity is prioritized.

---

### `XU.SVG.Progress.Pie (ProgressRatio, BackgroundColor, FillColor, StrokeColor)`

Renders a **solid pie-style** circular progress indicator.

- **Use case**: Compact circular progress in tight spaces.

---

### `XU.SVG.Progress.Donut (ProgressRatio, BackgroundColor, InnerBackgroundColor, InnerRadiusPCT, FillColor, StrokeColor, IfShowLabel, LabelTextColor)`

Renders a **donut chart** with a customizable inner hole and **center label**.

- **Use case**: Dashboard KPI cards with visual + textual emphasis.
- **Customizable**: Inner background color, hole size (`0–1`), label color.

---

### `XU.SVG.Progress.About ()`

Returns a formatted string with library metadata (version, author, functions, usage note).

Useful for documentation or auditing installed packages.

```dax
EVALUATE { XU.SVG.Progress.About() }
```

---

## Global Configuration

All components share three configuration functions for consistent theming:

- `XU.SVG.Progress.Config.BoxWidth()` → Default: `140`
- `XU.SVG.Progress.Config.BoxHeight()` → Default: `20`
- `XU.SVG.Progress.Config.FontFamily()` → Default: `"Arial"`

> 💡 **Tip**: Override these in your model to apply a unified style across all progress visuals.

---

## Important Note

⚠️ **All measures using these functions must have the `DataCategory` property set to `ImageUrl`** in Power BI:

1. Select the measure in the Fields pane
2. Go to **Measure tools** → **Data category**
3. Choose **Image URL**

Without this, Power BI will display the raw SVG string instead of rendering the image.

---

## Basic Usage

Create a new measure in Power BI:

```dax
Margin Progress = 
    XU.SVG.Progress.Capsule(
        [Margin Ratio],      // Should return 0–1
        "#E6E6E6",           // Background
        "#235EB5",           // Fill
        TRUE(),              // Show label
        "",                  // Auto label (percentage)
        "#000000"            // Label color
    )
```

Or use a donut for a KPI card:

```dax
Revenue Donut = 
    XU.SVG.Progress.Donut(
        [Revenue Ratio],
        "#E0E0E0",
        "white",
        0.6,                 // Inner hole = 60% of radius
        "#235EB5",
        "#000000",
        TRUE(),
        "#235EB5"
    )
```

---

## Model Requirements

- **Power BI September 2025 or later** (required for DAX UDF support)
- No external dependencies — all logic is self-contained
- Input `ProgressRatio` should be between `0` and `1` (e.g., `0.75` for 75%)

---

## Tips & Best Practices

- Use **`VAL` parameters**: All functions use explicit `VAL` mode for stable, predictable results.
- **Reuse configuration**: Modify `Config.*` functions once to update all visuals.
- **Avoid overuse**: SVG rendering is client-side; too many instances may impact report performance.

---

## License

MIT License.

---
