# team3 — Per-cell quantification of Pol II nuclear localisation

Hackathon project analysing confocal microscopy images of HEK293 cells expressing
Avi-tagged RNA Polymerase II. 

## Main finding

Per-cell analysis of n=11 transfected cells across 6 imaging fields shows that
overexpressed Avi-Pol II accumulates in the cytoplasm rather than the nucleus
(local ratio 0.47 ± 0.18). The signal is real — 25× above the antibody background
measured from the no-transfection control (Sample 2). This quantifies the
known localisation issue and motivates adding a nuclear localisation signal
(NLS) in future constructs.

## Pipeline

```
.czi file → split 4 channels
  ├── DAPI  → Cellpose (deep learning) → Nuclear mask
  └── Avi   → Otsu threshold + morphology cleanup → Avi mask
                                              ↓
                                  Boolean intersection
                                              ↓
                              Transfected nuclei (cells of interest)
                                              ↓
                              Per-cell ratios:
                                - Global: nucleus / image-wide outside
                                - Local:  nucleus / same cell's cytoplasm
```

## Key numbers (Sample 7, full system)

| Metric | Value | Interpretation |
|---|---|---|
| Transfected cells | n=11 across 6 fields | Sufficient for statistics |
| Global ratio (nucleus / outside) | 15.7× ± 8.9 | Signal is real, 10× above antibody background |
| Local ratio (nucleus / cytoplasm) | 0.47 ± 0.18 | Pol II is twice as concentrated in cytoplasm |
| Sample 2 background | 1.56× | Antibody-only control baseline |
| Pipeline false positives in 4 controls (S1, S2, S3, S5) | 0 / 95 nuclei | Pipeline doesn't over-call transfection |

## Setup (Mac)

```bash
python3.11 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Place `.czi` files in `data/` (not tracked by git).

## How to use

Open `notebooks/FINAL_pipeline.ipynb` and run cells sequentially. The pipeline
processes Sample 7 replicates (`7_1.czi` through `7_6.czi`), the antibody
control (`Sample2.czi`), and additional controls (`Sample1`, `Sample3`,
`Sample5`).

Outputs are saved to `results_v2/`:

- `FIGURE_1_pipeline_overview.png` — full pipeline visualisation (methods slide)
- `FIGURE_2_background_validation.png` — Sample 2 control vs Sample 7 (validation slide)
- `FIGURE_3_main_results.png` — per-cell ratios for n=11 cells (main result slide)
- `sample7_all_cells.csv` — raw per-cell measurements

## Repository structure

```
.
├── notebooks/
│   ├── FINAL_pipeline.ipynb         ← MAIN ANALYSIS (use this)
│   ├── 05_clean_pipeline.ipynb      (earlier per-sample version)
│   └── older notebooks              (development history)
├── results_v2/                       ← Final figures and tables
├── results/                          (earlier per-sample outputs)
├── data/                             (gitignored — raw .czi files)
├── requirements.txt
├── .gitignore
└── README.md
```

## Tools used

- **Cellpose** (Stringer et al., Nature Methods 2021) — pre-trained U-Net for
  nucleus segmentation
- **scikit-image** — Otsu thresholding, morphological operations, region properties
- **aicspylibczi** — `.czi` file reader
- **pandas, matplotlib** — data tables and visualizations

## Team3
