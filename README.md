# CSc 8830 Computer Vision, Module 3: Blurring in space and in frequency

Blur an image with a box or Gaussian filter in the **spatial domain** (direct convolution,
written from the definition) and in the **Fourier domain** (`IFFT(FFT(f) * FFT(h))`), and show
that the results are identical to floating-point precision. This is the convolution theorem:
`f * h  <->  F . H`.

## Files

| File | Purpose |
|---|---|
| `blur_filters.py` | Kernels, spatial convolution, FFT convolution, validation, command-line tool |
| `app.py` + `templates/index.html` | Flask web application (the demo) |
| `make_report.py` | Reruns every experiment, regenerates `results/*.png` and `report/Module3_Report.pdf` |
| `sample/` | Two test images from scikit-image (public domain) |
| `results/` | Figures and web-app screenshots used in the report |
| `report/Module3_Report.pdf` | Theory (proof, worked example) and experimental evidence |

## Setup

```bash
python -m venv .venv && source .venv/bin/activate     # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

## Run the web application

```bash
python app.py
# open http://127.0.0.1:5000
```

Pick a kernel (box or Gaussian), its size and sigma, and an image (sample or upload). The page shows
the spatial result, the Fourier result, their difference, the spectra, and error metrics. Untick
"Zero-pad before the FFT" to see the wrap-around error from circular convolution.

To host other assignments on the same site, add a route and a link in the `<nav>` of `templates/index.html`.

## Run from the command line

```bash
python blur_filters.py --input sample/cameraman.png --kernel gaussian --size 15 --sigma 2.5 --out results/
```

Prints `max |spatial - fourier|` (about 1e-13) and writes the blurred images, the amplified
difference image and the spectra to `results/`.

## Rebuild the report

```bash
pip install reportlab
python make_report.py --name "Your Name" --repo https://github.com/<you>/cv-module3-blur \
    --screens results/web_app_padded.png results/web_app_no_padding.png
```

## Key points

* Kernels are normalised (weights sum to 1) so brightness is preserved.
* Spatial convolution is not a library call: each kernel weight adds a shifted copy of the image.
  SciPy's `convolve2d` is used only as an independent check.
* The FFT route zero-pads image and kernel to at least `(H+K-1) x (W+K-1)`. Without this the DFT
  performs *circular* convolution and the blur wraps around the image edges.
* Nonlinear filters (median, bilateral) are not convolutions, so the theorem does not apply to them.

## Reference

S. K. Nayar, *Image Processing I* and *II*, First Principles of Computer Vision, Columbia University.
