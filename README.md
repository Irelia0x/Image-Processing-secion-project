# Image Processing Pipeline 🖼️

A unified Python pipeline that preprocesses, filters, and applies edge
detection & segmentation to the Gender Classification dataset.

---

## Project structure

```
image-processing-pipeline/
│
├── image_processing_pipeline.py   ← single entry-point script
├── README.md
├── report.pdf                     ← full technical report
└── requirements.txt
```

---

## Team division

| Person | Task | Output folder |
|--------|------|---------------|
| Person 1 | Dataset Collection | (input dataset) |
| Person 2 | Preprocessing | `stage1_preprocessed/` |
| Person 3 | Image Filtering | `stage2_filtered/` |
| Person 4 | Edge Detection & Segmentation | `stage3_edge_seg/` |
| Person 5 | Documentation & GitHub | This README + report |

---

## Dataset

**Gender Classification** by sheikhminan on Kaggle.
Path on Kaggle: `/kaggle/input/datasets/sheikhminan/gender-class/`

Expected folder structure inside the dataset:
```
gender-class/
  Training/
    female/
    male/
  Validation/
    female/
    male/
```

Upload the dataset **once** at the top of your Kaggle notebook — the script
reads every sub-folder automatically.

---


## Stage details

### Stage 1 — Preprocessing
- Loads each image as **grayscale** (removes colour redundancy)
- Resizes to **128 × 128** pixels with anti-aliasing
- Normalises pixel values to **[0, 1]** (skimage handles this automatically)
- Applies a mild **Gaussian smooth** (σ = 1) to reduce sensor noise
- Saves as `uint8` preserving the original folder tree

### Stage 2 — Image Filtering
- **Gaussian Blur** (5 × 5 kernel): suppresses Gaussian noise
- **Median Blur** (k = 5): removes salt-and-pepper noise without edge smearing
- **Sharpening** (unsharp-mask 3 × 3 kernel): boosts edges and fine detail
- The sharpened version is saved and passed to Stage 3

### Stage 3 — Edge Detection & Segmentation (sample of 1000 images)
- **Sobel**: gradient magnitude √(Gx² + Gy²) — noise-robust first-order edges
- **Canny**: Gaussian → gradient → non-max suppression → hysteresis (clean, thin edges)
- **Binary Threshold** (127): simple foreground / background segmentation
- All three outputs are saved per image; results are zipped automatically

---

## Configuration

All tunable parameters live at the top of `image_processing_pipeline.py`:

| Parameter | Default | Purpose |
|-----------|---------|---------|
| `RESIZE_DIM` | `(128, 128)` | Target image size |
| `GAUSS_SIGMA` | `1` | Stage 1 smoothing strength |
| `GAUSS_KERNEL` | `(5, 5)` | Stage 2 Gaussian kernel |
| `MEDIAN_K` | `5` | Stage 2 Median kernel |
| `SAMPLE_SIZE` | `1000` | Images processed in Stage 3 |
| `CANNY_T1 / T2` | `100 / 200` | Canny hysteresis thresholds |
| `THRESH_VALUE` | `127` | Binary segmentation cutoff |

---

## Output

```
/kaggle/working/
  stage1_preprocessed/   ← grayscale, resized, normalised images
  stage2_filtered/       ← sharpened images
  stage3_edge_seg/       ← *_sobel.jpg, *_canny.jpg, *_seg.jpg
  final_results.zip      ← downloadable archive of Stage 3
```

---

## License

For academic / educational use only.
