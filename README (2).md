# Photo Editing Module for SRH ImageViewer

A plug-in module for the **SRH ImageViewer** (PySide6 desktop app), written for the Imaging Technologies capstone at SRH Berlin. It adds a control panel with 12 image-processing operations, including a set of photo-editing adjustments (brightness, contrast, exposure, saturation, warmth, vignette, sharpness and noise reduction) implemented from scratch with NumPy and SciPy.

<!-- Add a screenshot of the app running this module here, e.g.: ![Kenneth module in the app](images/app.png) -->

## Operations

**Photo-editing adjustments (added by me)**

| Operation | Parameter (range) | How it works |
|---|---|---|
| Brightness | beta (-100 to 100) | Adds a constant to every pixel and clips to 0-255 |
| Contrast | alpha (0 to 3) | Scales intensity around mid-gray: `alpha * (I - 128) + 128` |
| Exposure | stops (-3 to 3) | Multiplies intensity by `2^stops`, like camera exposure stops |
| Saturation | scale (0 to 3) | Blends each pixel with its luminance (0.299 R + 0.587 G + 0.114 B) |
| Warmth | warmth (-50 to 50) | Raises red and lowers blue (or the reverse) for a warm or cool tone |
| Vignette | intensity (0 to 1) | Darkens pixels with a radial mask based on distance from the center |
| Sharpness | amount (0 to 2) | 3x3 Laplacian-based sharpening kernel, blended with identity below 1 |
| Noise Reduction | strength (1 to 5) | Mean filter with a `(2*strength+1)` square kernel |

**Standard operations (from the project template)**

| Operation | Parameter | Notes |
|---|---|---|
| Gaussian Blur | sigma (0.1 to 25) | `skimage.filters.gaussian` |
| Sobel Edge Detect | none | Converts to grayscale, then applies the Sobel filter |
| Power Law (Gamma) | gamma (0.01 to 5) | Normalizes, applies the exponent and rescales |
| Convolution | custom 3x3 kernel | Applied per channel with `scipy.ndimage.convolve` |

## Tech stack

Python, PySide6 (UI), NumPy, SciPy, scikit-image, imageio.

## How to use it

This module plugs into the course framework, so it needs the ImageViewer project:

1. Clone the project: `git clone https://github.com/sukhalrii/srh-image-viewer.git`
2. Copy `kenneth_module.py` and `__init__.py` from this repo into `src/modules/kenneth/` in that project.
3. Install the dependencies: `pip install -r requirements.txt`
4. Run the app from the `src` folder:

```bash
cd src
python main_app.py
```

5. Choose **Kenneth Module** in the module dropdown, open an image, pick an operation, set its parameter and click **Apply Processing**.

## Code structure

- `*ParamsWidget` classes: one small parameter UI per operation. `GenericSliderParamsWidget` is reused for all the single-value adjustments.
- `KennethControlsWidget`: the control panel. It holds an operation dropdown and a stacked widget that shows the matching parameter UI.
- `KennethImageModule`: implements the framework's `IImageModule` interface (`load_image`, `process_image`, supported formats). All the image-processing logic lives in `process_image`.

## Known limitations

- The adjustments assume 8-bit images (values clipped to 0-255).
- Noise Reduction is a simple mean filter, so it blurs edges. A median or bilateral filter would preserve them better.
- Sharpness values other than 1 also change overall brightness, because the kernel no longer sums to 1.
- Sobel output is a single-channel float image.

## Credits

Built on the SRH ImageViewer framework from the Imaging Technologies capstone course. The framework, module interface and template operations come from the course project. The adjustment operations and control panel in `kenneth_module.py` are my contribution.

Author: Kenneth Obisi, Applied Mechatronics Engineering, SRH Berlin
