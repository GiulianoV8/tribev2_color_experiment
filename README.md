<div align="center">

# Color Concept Encoding Experiment

**Does the brain represent color the same way whether you *read*, *see*, or *hear* it?**

An in-silico neuroscience experiment using [TRIBE v2](https://github.com/facebookresearch/tribev2) (Meta's Trimodal Brain Encoder)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/facebookresearch/tribev2/blob/main/tribe_demo.ipynb)
[![Python 3.11+](https://img.shields.io/badge/python-3.11%2B-blue.svg)](https://www.python.org/downloads/)
[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)

</div>

## Overview

This project investigates how the brain encodes **color concepts** when they arrive through different sensory channels. Using TRIBE v2's pretrained brain encoding model, we predict fMRI cortical responses for four colors (red, blue, green, yellow) across three modalities:

| Condition | Stimulus | What the model sees |
|-----------|----------|---------------------|
| **Text** | Video of the written word on a gray background | `"blue"` as white text |
| **Color** | Video of a full-screen solid color patch | A pure blue rectangle |
| **Audio** | Spoken word via gTTS → WhisperX pipeline | The spoken word `"blue"` |

**Core question:** Does TRIBE predict similar neural activation patterns for the *concept* "blue" regardless of whether it is delivered as text, color, or sound?

## Analysis Pipeline

The experiment notebook (`experiment/Tribe_color_experiment.ipynb`) follows these steps:

1. **Stimulus generation** — Programmatically create text videos, color-patch videos, and TTS audio for each color concept
2. **TRIBE inference** — Run each stimulus through the pretrained model to obtain predicted fMRI responses (~20k vertices on fsaverage5)
3. **Preprocessing** — Time-average predicted responses per stimulus
4. **ROI aggregation** — Summarize vertex-level predictions into HCP atlas parcels
5. **Condition contrasts** — Compute within-parcel pairwise differences (text vs. color, text vs. audio, audio vs. color)
6. **Pattern similarity** — Pearson correlation of parcel activation profiles across modalities
7. **RSA** — Representational Similarity Analysis at both the vertex and parcel level, comparing how each modality organizes the four color concepts
8. **Visualization** — Bar plots, scatter plots, RSM heatmaps, and brain surface maps (nilearn)

## Project Structure

```
tribev2_color_experiment/
├── README.md
├── pyproject.toml
├── tribe_demo.ipynb          # Original TRIBE v2 demo notebook
│
├── experiment/
│   ├── Tribe_color_experiment.ipynb   # Main experiment notebook
│   ├── stimuli/              # Generated stimuli (text/color/audio × color)
│   │   ├── red_text.mp4
│   │   ├── red_color.mp4
│   │   ├── red_audio.mp4 / .txt
│   │   ├── blue_…  green_…  yellow_…
│   │   └── …
│   └── results/
│       ├── tribe_predictions.pkl      # Cached model predictions
│       └── parcel_data.csv            # ROI-level aggregated data
│
└── tribev2/                  # TRIBE v2 library (upstream)
    ├── main.py               # Experiment pipeline: Data, TribeExperiment
    ├── model.py              # FmriEncoder: Transformer-based multimodal→fMRI
    ├── pl_module.py          # PyTorch Lightning training module
    ├── demo_utils.py         # TribeModel & helpers for inference
    ├── eventstransforms.py   # Custom event transforms (word extraction, chunking)
    ├── utils.py              # Multi-study loading, splitting, subject weighting
    ├── utils_fmri.py         # Surface projection (MNI / fsaverage) & ROI analysis
    ├── grids/                # Experiment configs & test entry point
    ├── plotting/             # Brain visualization (PyVista & Nilearn backends)
    └── studies/              # Dataset definitions (Algonauts2025, Lahner2024, …)
```

## Getting Started

### Prerequisites

- Python ≥ 3.11
- A [Hugging Face](https://huggingface.co/) account with the Llama-3.2 license accepted (required by TRIBE v2)

### Installation

```bash
# Clone the repo
git clone https://github.com/<your-username>/tribev2_color_experiment.git
cd tribev2_color_experiment

# Install TRIBE v2 with plotting support
pip install -e ".[plotting]"

# Or install from upstream if needed
pip install "tribev2[plotting] @ git+https://github.com/facebookresearch/tribev2.git"
```

### Running the Experiment

Open the notebook in Jupyter or Google Colab:

```bash
jupyter notebook experiment/Tribe_color_experiment.ipynb
```

The notebook is self-contained — it generates stimuli, runs inference, and produces all figures. On first run, the TRIBE v2 checkpoint (~1 GB) is downloaded from Hugging Face.

## Key Dependencies

- [TRIBE v2](https://github.com/facebookresearch/tribev2) — Multimodal brain encoding model
- [nilearn](https://nilearn.github.io/) — Brain surface visualization
- [MNE-Python](https://mne.tools/) — HCP atlas parcellation
- [gTTS](https://gtts.readthedocs.io/) — Text-to-speech for audio stimuli

## Citation

This experiment builds on the TRIBE v2 model. If you use this work, please cite:

```bibtex
@article{dAscoli2026TribeV2,
  title={A foundation model of vision, audition, and language for in-silico neuroscience},
  author={d'Ascoli, St{\'e}phane and Rapin, J{\'e}r{\'e}my and Benchetrit, Yohann and Brookes, Teon and Begany, Katelyn and Raugel, Jos{\'e}phine and Banville, Hubert and King, Jean-R{\'e}mi},
  year={2026}
}
```

## License

This project is licensed under CC-BY-NC-4.0. See [LICENSE](LICENSE) for details.
