🌐 **English** | [日本語](./README.ja.md)

# Decorate the screen -Dekosuku-

![ ](./img/decosuku.jpg)

## Overview

- `Decorate the screen -Dekosuku-` is an application that converts your favorite illustrations or photos into sticker‑style SVGs, allowing you to freely decorate your desktop. The stickers appear as if they are attached to your wallpaper, without interfering with desktop icons.
- It requires the external bitmap tracing tool [Vectrace](https://www.vector.co.jp/soft/winnt/art/se529086.html). Extract the archive and place the resulting `vectrace.exe` in the same folder as `decorator.exe`.
- It includes advanced image‑processing algorithms such as bilateral filter and k‑means clustering, providing high‑quality noise reduction and color‑quantization.
- The application consists of two tools:
  - `decorator.exe`: creates sticker‑style images.
  - `wallview.exe`: monitors the desktop wallpaper and manages the rendering area as a background resident program.

## Supported Environments

| Item | Details |
| --- | --- |
| Supported OS | Windows 10 / 11 |
| Architecture | 32‑bit application (runs on 64‑bit Windows as well) |
| Required Software | Microsoft Edge (uses WebView2 Runtime for SVG display) |
| External Tool | [Vectrace](https://www.vector.co.jp/soft/winnt/art/se529086.html) (bitmap tracing) |
| Recommended Input Image Size | ≤ 680×680 pixels |

## Installation

1. Extract the distribution archive to a folder of your choice.
2. Download [Vectrace](https://www.vector.co.jp/soft/winnt/art/se529086.html) (or from [GitHub](https://github.com/nyorotan/vectrace)) and place the extracted `vectrace.exe` alongside `decorator.exe`.
3. Ensure Microsoft Edge is installed (it comes pre‑installed on Windows 10/11).
4. Launch either `decorator.exe` (to create stickers) or `wallview.exe` (to place stickers on the wallpaper).

![ ](./img/00.jpg)

## Directory Structure

```
Decorate/
├── assets/                 ... sample images, icons, cursors, etc.
├── lib/                    ... Go DLLs (image processing, color reduction)
│   ├── image_tool_x86.dll
│   └── reduction_x86.dll
├── tmp/                    ... temporary files (auto‑generated & auto‑deleted)
├── utils/                  ... configuration files
│   ├── color_palette.json  ... saved color palettes
│   ├── autosave.json       ... automatic layout save file
│   └── exported_image.png  ... exported image for wallpaper composition
├── decorator.exe           ... main sticker‑creation tool
├── svgbase.exe             ... SVG layout editor (WebView2 based)
├── wallview.exe            ... wallpaper monitor & renderer
├── vectrace.exe            ... external bitmap‑to‑SVG tracer (must be obtained separately)
├── hspext.dll              ... HSP extension plugin
├── LICENSE                 ... MIT License file
├── CREDITS                 ... third‑party library licenses
├── CHANGELOG.md            ... change history
├── README.md               ... this document (English version)
└── README-viewer.exe       ... viewer specialized for README.md
```

*`README-viewer.exe` is a separate free utility released by the author at [Vector](https://www.vector.co.jp/soft/winnt/util/se529125.html) and on [GitHub](https://github.com/nyorotan/readme-viewer).*.

## Caveats

### Application Limitations

- Raster‑to‑vector conversion (bitmap to SVG) transforms pixel‑based raster data into mathematically described vector data. Because of inherent limitations, the result cannot be *exactly* identical to the original image, and complex or high‑color‑count images may not work well.
- HSP3 is a 32‑bit application, so the bundled DLLs are also 32‑bit. Even on 64‑bit systems, memory usage is limited. Large images or very complex SVG paths may cause instability or out‑of‑memory errors. **We recommend input images no larger than 680×680 pixels.**
- SVG rendering relies on WebView2. As long as Microsoft Edge is installed, it should work.
- Processing time depends on image size and the complexity of selected algorithms. Bilateral filters and k‑means can be slow.
- The app manipulates the desktop drawing layer, which may conflict with other desktop‑customization tools. Use with caution when combining with other utilities.

### About Vectrace

Vectrace is a command‑line bitmap‑to‑SVG conversion tool written in Go. Due to licensing, it cannot be bundled with this app; you must download it from [Vector](https://www.vector.co.jp/soft/winnt/art/se529086.html) or its [GitHub repository](https://github.com/nyorotan/vectrace).

### What is an SVG?

SVG stands for *Scalable Vector Graphics*, a vector‑format image that remains crisp at any zoom level. This tool converts processed images into SVG so they can be used as resolution‑independent graphics.

## Features

- Although the workflow looks complex, it is simply a matter of selecting filters step‑by‑step while previewing the result.
- The core application is written in `HSP3`, while performance‑critical parts (filters, etc.) are implemented in Go DLLs/executables.
- Creating a sticker‑style SVG involves five sections, each applying a specific filter:
  - **Noise Reduction**: reduces photographic noise for smoother texture.
  - **Color Quantization**: reduces the number of colors, giving an illustration‑like effect.
  - **Edge Enhancement**: detects and emphasizes edges.
  - **Color Adjustment**: modifies saturation for vividness.
  - **Transparency Processing**: makes selected background areas transparent.
- To keep CPU load low, the app does not place the raw SVG directly on the desktop. Instead, it composes a PNG that overlays the wallpaper, allowing smooth operation even on modest hardware.
- Stickers are placed **between** the wallpaper and desktop icons, so they do not interfere with other tasks.
- Multi‑monitor setups are supported; you can choose which monitor to decorate.

## Usage

### 1. Create Stickers

#### Launch `decorator.exe`

![ ](./img/01.jpg)

1. Click **Open Image** and select the image you wish to process (recommended size ≤ 680×680).
2. Use the preview window to apply filters. Scroll to zoom, drag with the right mouse button to move, click the center mouse button to reset view.
3. Complete the five sections in order. Completed sections receive a checkmark.
4. Choose a filter for each section (only **one filter per section** is allowed). Filters cannot be stacked.
   - Some filters expose adjustable parameters.
   - Sample images are shown in small preview windows.
   - Press **Apply** to see the filtered result in the preview. Repeat until satisfied.
5. After applying a filter, click **Next** to proceed to the next section.

![ ](./img/02.jpg)

#### Choose Sticker Style

- **Sticker**: directly converts the preview image; optional white border for a classic sticker look.
- **Can Badge**: creates a circular badge‑style sticker with a selectable background color.
- **Sparkle Seal**: applies a sparkling‑seal effect; you can choose the seal color and intensity.

When the sticker is finished, a new window displays the result, supporting zoom and pan.

#### Transparency Editing

![ ](./img/03.jpg)

- **Shift** toggles between *Auto* and *Eraser* modes.
- **Ctrl** changes the operation (add vs. remove) while editing.
- **Auto**: click to select a similar color (green overlay). Drag with Ctrl to add to the selection (red overlay).
- **Eraser**: drag to erase (green). Drag with Ctrl to restore (red).
- **Space** key shows transparent regions in magenta with dashed borders.
- **Save Image** exports the preview as PNG (including alpha).
- **Save Mask** exports a binary mask (black‑white) for use in other applications.

### 2. Arrange Stickers

#### Launch `wallview.exe`

![ ](./img/05.jpg)

- Right‑click the tray icon and select **Rearrange**.
- Load the stickers you created and place them freely. SVG stickers from other tools can also be imported.
- Click the **×** button (appears in the top‑right when you move the cursor) or select **Exit** from the tray menu to close. The layout is saved automatically and applied to the desktop wallpaper.
- You can also manually save the current layout as a JSON file via the tray menu and reload it later.

### 3. Display Stickers

![ ](./img/06.jpg)

`wallview.exe` automatically loads the saved layout on start. If you have multiple monitors, you can select which monitor to display stickers on via the tray menu. Changing the Windows wallpaper triggers an automatic refresh.

## Detailed Section Descriptions

### 1. Noise Reduction

- **Averaging**: smooths neighboring pixels to reduce grain.
- **Gaussian**: uses a Gaussian blur for natural‑looking noise removal.
- **Bilateral**: smooths while preserving edges (computationally intensive and memory‑heavy).
- **Kuwahara**: smooths within regions while keeping edges, giving a painterly effect.

> **Note:** Bilateral filtering yields the best quality but can be slow; Kuwahara is a faster alternative.

### 2. Color Quantization

> **This section is mandatory; you cannot select "None".**

- **Median Cut**: efficiently selects representative colors for balanced quantization.
- **k‑means**: performs statistical clustering to compute optimal colors.
- **Posterization**: forces a limited number of tonal steps, creating an illustration‑like flat look.

Generally, **Median Cut** offers stable results, while **k‑means** can produce higher‑quality palettes at the cost of longer processing time and variability.

### 3. Edge Enhancement

> Sample images are shown in black‑and‑white for clarity, but the actual process extracts edges from the original color image.

- **Sobel**: computes gradient magnitude to extract outlines.
- **Laplacian**: uses second‑order derivatives to find rapid intensity changes.
- **Canny**: produces thin, noise‑resistant edges.
- **Unsharp Mask**: sharpens edges by subtracting a blurred version.

> In most cases, leaving this section at "None" works fine. Use it when you want a more illustrated or painterly effect.

### 4. Color Adjustment

Use this when the quantization step has reduced brightness or saturation and you wish to fine‑tune the colors.

### 5. Transparency Processing

- **Auto**: quickly removes large background areas.
- **Eraser**: fine‑tunes the mask.
- If you erase too much, hold **Ctrl** while in Eraser mode to restore areas (cursor turns red).

## Color Picker

![ ](./img/07.jpg)

- **Palette**: save up to 10 frequently used colors.
  - Right‑click a palette slot to **save** the current color.
  - Left‑click a slot to **load** the saved color.
- **Eyedropper**: pick any screen color.
  1. Click **Pick**.
  2. Cursor changes to a crosshair; the color under the cursor is shown in real‑time.
  3. Left‑click to apply the color to the picker.
  4. Press **Esc** to cancel.

## Keyboard & Mouse Controls

### Preview Window (`decorator.exe`)

| Action | Effect |
|---|---|
| Mouse scroll | Zoom in/out |
| Right‑drag | Move image |
| Center click (wheel click) | Reset view |

### Transparency Mode

| Action | Effect | Display Color |
|---|---|---|
| **Shift** | Toggle Auto / Eraser mode |
| **Ctrl** (while pressed) | Switch operation (Add ↔ Remove) |
| **Space** (while pressed) | Show transparent areas in magenta with dashed borders |

#### Auto Mode

| Action | Effect | Display Color |
|---|---|---|
| Left click | Select similar color to make transparent | Green |
| Ctrl + drag | Expand selection area | Red |

#### Eraser Mode

| Action | Effect | Display Color |
|---|---|---|
| Drag | Erase selected area | Green |
| Ctrl + drag | Restore erased area | Red |

## Data Saving & Restoration

### In `decorator.exe`

- **Save Image**: exports the preview as a PNG (preserves alpha channel).
- **Save Mask**: exports a binary mask (black‑white) for use in other applications.

### In `wallview.exe`

- **Auto‑save**: layout is saved to `wallview/utils/autosave.json` when the editor is closed and loaded automatically on next start.
- **Manual save**: via the tray menu, you can save the current layout as a JSON file anywhere you like.
- **Restore**: load a previously saved JSON layout from the tray menu.

## Troubleshooting

| Symptom | Cause & Solution |
|---|---|
| "Vectrace not found" | Ensure `vectrace.exe` is placed in the same folder as `decorator.exe`. Download from [Vectrace](https://www.vector.co.jp/soft/winnt/art/se529086.html). |
| Slow processing | Bilateral filter and k‑means are computationally heavy. Reduce image size or use lighter filters like Kuwahara or Median Cut. |
| Out‑of‑memory error | As a 32‑bit app, memory is limited. Use images ≤ 680×680 pixels. |
| SVG not displayed | Verify Microsoft Edge (WebView2 Runtime) is installed. |
| Stickers not applied to wallpaper | Ensure `wallview.exe` is running and its tray icon is visible. |
| Stickers disappear after changing wallpaper | `wallview.exe` automatically detects wallpaper changes and redraws; a short delay may occur. |
| Conflicts with other desktop‑customization tools | This app manipulates the desktop drawing layer; use caution when running alongside other utilities. |

## Technical Information

| Component | Technology | Description |
|---|---|---|
| `decorator.exe` | HSP3 | Main UI & workflow control |
| `wallview.exe` | HSP3 | Wallpaper monitor & tray‑resident |
| `svgbase.exe` | Go + WebView2 | SVG layout editor (HTML/CSS/JS) |
| `image_tool_x86.dll` | Go (cgo) | Image preview, transparency, SVG display |
| `reduction_x86.dll` | Go (cgo) | Noise reduction, color quantization, edge detection, color correction |
| `vectrace.exe` | Go | Bitmap‑to‑SVG conversion (external) |

## License

This project is released under the **MIT License**. You may use it freely for commercial or non‑commercial purposes.

- **Copyright**: © 2026 nyorotan
- **Vectrace**: Due to licensing, Vectrace is not bundled. Download from [Vector](https://www.vector.co.jp/soft/winnt/art/se529086.html) or its [GitHub repository](https://github.com/nyorotan/vectrace).

## Version Information

- **Version**: v1.0.2
- **Author**: nyorotan
