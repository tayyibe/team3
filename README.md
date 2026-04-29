# team2

Hackathon project: automated analysis of microscopy images
for RNA Polymerase II 

## Setup (Mac)

```bash
python3.11 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Place `.czi` files in `data/`.

## Pipeline

`.czi` → split channels → max projection → nucleus segmentation (Cellpose) → per-cell measurements → ratios & plots