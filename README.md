# DGTRS-CLIP — Reproducibility Study
### Short-Text Cross-Modal Retrieval on RSITMD


This repository contains our reproducibility study of **DGTRS-CLIP**, a dual-granularity vision-language foundation model for remote sensing, proposed by Chen et al. (2025). We reproduce the Short-Text Cross-Modal Retrieval (STCR) results on the **RSITMD** benchmark using the publicly released ViT-B/16 checkpoint.

---

## 📄 Paper Reference

> Chen, W., Wei, J., Deng, Y., et al. (2025). **DGTRSD & DGTRS-CLIP: A Dual-Granularity Remote Sensing Image–Text Dataset and Vision Language Foundation Model for Alignment**. *IEEE Journal of Selected Topics in Applied Earth Observations and Remote Sensing*, 18, 29113–29130.

- 📦 Official Repo: https://github.com/MitsuiChen14/DGTRS
- 📝 arXiv: https://arxiv.org/abs/2503.19311

---

## 📁 Repository Structure

```
DGTRS/
├── checkpoints/
│   └── DGTRS-CLIP-ViT-B-16.pt        # Downloaded from HuggingFace
├── model/
│   ├── __init__.py
│   ├── longclip.py                    # Model loading & inference interface
│   ├── model_longclip.py              # Core architecture (KPS + dual encoder)
│   ├── simple_tokenizer.py            # BPE tokenizer
│   └── __pycache__/
├── data/
│   └── dataset_RSITMD.json            # RSITMD annotations
├── images/
│   └── rsitmd/
│       ├── baseballfield_452.tif
│       └── ... (4743 .tif files)
├── eval.ipynb                         # Our evaluation notebook
└── README.md
```

---

## ⚙️ Setup

### 1. Clone this repo

```bash
git clone https://github.com/Mateen880/DGTRS-Remote-Sensing-VLM-Evaluation.git
cd DGTRS-Remote-Sensing-VLM-Evaluation
```

### 2. Install dependencies

```bash
pip install torch torchvision Pillow numpy
```

> **No GPU required.** The evaluation runs on CPU. Expect ~30–45 minutes on a mid-range CPU (e.g., Core i5 8th Gen).

### 3. Download the model checkpoint

Download `DGTRS-CLIP-ViT-B-16.pt` from HuggingFace and place it in `./checkpoints/`:

```
https://huggingface.co/MitsuiChen14/DGTRS-CLIP-ViT-B-16
```

### 4. Download the RSITMD dataset

Download from the original authors' repository and place files as shown in the structure above:

```
https://github.com/xiaoyuan1996/AMFMN/tree/master/RSITMD
```

---

## 🚀 Running Evaluation

Open `eval.ipynb` in Jupyter and run all cells. The notebook will:

1. Load the DGTRS-CLIP-ViT-B-16 model
2. Parse the RSITMD test split (452 images, 2260 captions)
3. Encode all images and captions
4. Compute the (452 × 2260) cosine similarity matrix
5. Report Recall@1, @5, @10 for both Image→Text and Text→Image directions

**If you get `ModuleNotFoundError: No module named 'model'`**, add this at the top of the first cell:

```python
import sys, os
sys.path.insert(0, "/path/to/DGTRS")
os.chdir("/path/to/DGTRS")
```

---

## 📊 Reproduced Results

Results on **RSITMD test split** with **DGTRS-CLIP ViT-B/16**:

| Metric | Paper | Ours | Δ |
|--------|-------|------|---|
| T→I  R@1 | 15.18% | 15.22% | +0.04% |
| T→I  R@5 | 40.80% | 40.97% | +0.17% |
| T→I  R@10 | 55.58% | 55.22% | -0.36% |
| I→T  R@1 | 21.90% | 21.02% | -0.88% |
| I→T  R@5 | 39.82% | 38.94% | -0.88% |
| I→T  R@10 | 52.21% | 51.99% | -0.22% |
| **mR** | **37.58%** | **37.23%** | **-0.35%** |

✅ Maximum deviation: **0.88%** — successful reproduction confirmed.

---

## 🔑 Key Implementation Notes

- **L2 normalization is critical** — normalize both image and text features before computing cosine similarity. This is not shown in the official demo but is essential for correct retrieval metrics.
- **Filter test split** — use only `split == "test"` entries from the JSON (452 images).
- **JSON structure** — top-level key is `"images"`, sentences are under `"sentences"` → `"raw"`.
- **TIF format** — PIL handles `.tif` natively; install `tifffile` if you encounter decoding errors.

---

## 💻 Hardware Used

| Component | Spec |
|-----------|------|
| CPU | Intel Core i5 8th Generation |
| GPU | None |
| RAM | 16 GB |
| Python | 3.12.4 |

---

## 📚 Citation

If you use this reproduction or the original DGTRS-CLIP model, please cite:

```bibtex
@article{chen2025dgtrs,
  title={DGTRSD \& DGTRS-CLIP: A Dual-Granularity Remote Sensing Image--Text Dataset and Vision Language Foundation Model for Alignment},
  author={Chen, Weizhi and Wei, Jin and Deng, Yupeng and Chen, Jingbo and Chen, Jiansheng and Feng, Yuman and Xi, Zhihao and Liu, Diyou and Li, Kai and Meng, Yu},
  journal={IEEE Journal of Selected Topics in Applied Earth Observations and Remote Sensing},
  volume={18},
  pages={29113--29130},
  year={2025}
}
```
