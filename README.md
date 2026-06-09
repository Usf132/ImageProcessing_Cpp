# 🖼️ Image Processing with Matrices

A C++ project that implements common image processing operations from scratch using a custom 3D matrix-based `Image` class. Supports loading and saving PPM (P3) files and applying a range of pixel-level transformations.

---

## Features

| Operation | Description |
|---|---|
| **Grayscale** | Converts RGB to single-channel using the luminance formula |
| **Flip Horizontal** | Mirrors the image left-to-right |
| **Flip Vertical** | Mirrors the image top-to-bottom |
| **Brightness** | Adds a positive or negative offset to every pixel |
| **Contrast** | Scales pixel values around the midpoint (128) |
| **Blur** | Applies a 3×3 box filter with border clamping |
| **Rotate 90°** | Rotates the image clockwise, correctly handling non-square images |

---

## Getting Started

### Prerequisites

- A C++17-compatible compiler (e.g. `g++`, `clang++`)

### Build

```bash
g++ -std=c++17 -Wall -o image_processor main.cpp
```

### Run

```bash
./image_processor
```

This will:
1. Generate a 4×4 test image (`test_image.ppm`)
2. Apply all transformations and save each result as a `.ppm` file
3. Print pixel data for each operation to the console

### Output Files

```
test_image.ppm
gray_image.ppm
flipped_horizontal.ppm
flipped_vertical.ppm
bright_image.ppm
contrast_image.ppm
blurred_image.ppm
rotated90_image.ppm
```

> PPM files can be viewed with tools like [GIMP](https://www.gimp.org/), [IrfanView](https://www.irfanview.com/), or converted to PNG/JPG using ImageMagick:
> ```bash
> convert input.ppm output.png
> ```

---

## Project Structure

```
.
├── main.cpp        # Image class + all transformation functions + main driver
└── README.md
```

### `Image` Class

The `Image` class stores pixel data as a `vector<vector<vector<int>>>` in `[height][width][channel]` order.

```cpp
Image img(width, height, channels);  // create blank image
img.loadPPM("file.ppm");             // load from disk
img.savePPM("output.ppm");           // save to disk
img(y, x, channel);                  // pixel access
```

---

## Transformation Details

### Grayscale
Uses the standard luminance-weighted formula:
```
gray = 0.299·R + 0.587·G + 0.114·B
```
Input must be a 3-channel RGB image; a guard returns the original unchanged if not.

### Blur
Uses a 3×3 averaging kernel. Border pixels are handled by **clamping** neighbor coordinates to the valid image bounds, avoiding the black-border artifact that appears when border pixels are skipped.

### Rotate 90°
For a W×H input image, the output dimensions are H×W. Each pixel maps as:
```
output(x, height - 1 - y) = input(y, x)
```

---

## Tests

A self-contained test suite is embedded in the source. Run it by compiling with `#define RUN_TESTS` or by using the test build target. The suite covers **35 cases** across all 7 operations, including:

- Exact pixel value checks (e.g. grayscale luma values)
- Clamping at 0 and 255 for brightness and contrast
- Border/corner pixel correctness for blur
- Dimension correctness for non-square rotation
- Identity checks (double-flip, 4× rotate)

**All 35 tests pass.**

---

## Implementation Notes

- All transformations return a **new `Image`** — originals are never modified.
- Pixel values are clamped to `[0, 255]` wherever arithmetic could overflow.
- The `savePPM` function handles both 1-channel (grayscale) and 3-channel (RGB) images, writing grayscale values to all three PPM channels automatically.

---

## License

MIT
