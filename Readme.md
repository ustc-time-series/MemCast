<div align="center">
  <h1><img src="assets/image.png" alt="MemCast logo" style="height: 1em; width: auto; vertical-align: -0.15em; margin-right: 0.4em;">MemCast: Memory-Driven Time Series Forecasting with Experience-Conditioned Reasoning (ICML 2026)</h1>
  <a href="./LICENSE">
    <img src="https://img.shields.io/badge/license-MIT-green" alt="License">
  </a>
  <a href="https://github.com/Xiaoyu-Tao/MemCast-TS/stargazers">
    <img src="https://img.shields.io/github/stars/Xiaoyu-Tao/MemCast-TS?style=social" alt="Stars">
  </a>
  <a href="https://github.com/Xiaoyu-Tao/MemCast-TS/pulls">
    <img src="https://img.shields.io/badge/PRs-Welcome-green" alt="PRs Welcome">
  </a>

</div>

MemCast is a novel framework that reformulates **Time Series Forecasting (TSF)** as an **experience-conditioned reasoning task**. It explicitly accumulates forecasting experience into a hierarchical memory and leverages it to guide the inference process of Large Language Models (LLMs).

> 📝 “MemCast: Memory-Driven Time Series Forecasting with Experience-Conditioned Reasoning”  
> **ICML 2026** | [📄 Proceedings](https://proceedings.mlr.press/v306/tao26a.html) | [PDF](https://raw.githubusercontent.com/mlresearch/v306/main/assets/tao26a/tao26a.pdf) | [arXiv](https://arxiv.org/abs/2602.03164) | [OpenReview](https://openreview.net/forum?id=IoY0A6opfl)
>
> Proceedings of the 43rd International Conference on Machine Learning, **PMLR 306:119553–119569, 2026**.

## 📰 News

- **[2026-10-15] Publication update:** MemCast is published in the **ICML 2026** proceedings (**PMLR 306**, pp. **119553–119569**). This README now includes the official [proceedings entry](https://proceedings.mlr.press/v306/tao26a.html), [PDF](https://raw.githubusercontent.com/mlresearch/v306/main/assets/tao26a/tao26a.pdf), and conference BibTeX.
- **[2026-10-15] Dataset update:** Updated the [dataset download link](https://drive.google.com/file/d/17q-XD5Hrhr2j0Hg9O7vHAWgwwLA2CQ23/view?usp=drive_link) and preparation instructions, including data provenance and the chronological **8:2 memory/test split**.

---

> 🙋 Please let us know if you find out a mistake or have any suggestions!
> 
> 🌟 If you find this resource helpful, please consider starring this repository and citing our research.

## Citation

Please cite the official ICML 2026 proceedings version:

```bibtex
@inproceedings{tao2026memcast,
  title={{M}em{C}ast: Memory-Driven Time Series Forecasting with Experience-Conditioned Reasoning},
  author={Tao, Xiaoyu and Cheng, Mingyue and Guo, Ze and Yu, Shuo and Liu, Yaguo and Liu, Qi and Wang, Shijin},
  booktitle={Proceedings of the 43rd International Conference on Machine Learning},
  pages={119553--119569},
  year={2026},
  volume={306},
  series={Proceedings of Machine Learning Research},
  publisher={PMLR},
  url={https://proceedings.mlr.press/v306/tao26a.html}
}
```

## 🔍 Overview

Existing LLM-based forecasting methods often treat instances as isolated reasoning tasks, lacking explicit experience accumulation and continual evolution. MemCast introduces a new learning-to-memory paradigm:

- **Experience Accumulation**: Organizes training data into a **Hierarchical Memory** consisting of historical patterns, reasoning wisdom, and general laws.
- **Experience-Conditioned Reasoning**: Retrieves relevant patterns to guide reasoning, utilizes wisdom for trajectory selection, and applies general laws for reflective iteration.
- **Continual Evolution**: Enables dynamic confidence adaptation to update memory entry confidence during inference without test data leakage.

<p align="center">
  <img src="assets/main.png" width="800">
</p>

---

## ✨ Key Features

-  ✅ **Hierarchical Memory**: Structured storage of trend summaries, reasoning trajectories, and physical constraints.
-   🔗 **Wisdom-Driven Exploration**: Samples multiple reasoning paths and selects the optimal trajectory based on semantic consistency.
-   🔄 **Rule-Based Reflection**: Iterative refinement mechanism that enforces domain-specific constraints (e.g., non-negativity).
-   📊 **Dynamic Adaptation**: A confidence adaptation strategy that allows the model to evolve continuously in non-stationary environments.
-   🏆 **SOTA Performance**: Consistently outperforms deep learning and LLM-based baselines on Energy, Electricity, and Weather benchmarks.

---

## 🚀 Getting Started

### 1. Clone the repo

```bash
git clone https://github.com/Xiaoyu-Tao/MemCast-TS
cd MemCast
```

### 2. Environment Setup

```bash
conda create -n memcast python=3.10
conda activate memcast
pip install -r requirements.txt
```

### 3. Prepare Data
MemCast is evaluated on diverse real-world datasets with rich contextual features:
Energy (NP, PJM, BE, FR, DE) 
Electricity (ETTh1, ETTm1) 
Renewable Power (Windy Power, Sunny Power) 
Hydrology (MOPEX) 
Download the datasets used in our experiments from [Google Drive (memcast_dataset.zip)](https://drive.google.com/file/d/17q-XD5Hrhr2j0Hg9O7vHAWgwwLA2CQ23/view?usp=drive_link) and extract the 10 CSV files into `./datasets/`.

The datasets follow those used in AlphaCast. ETTh1 and ETTm1 are manually cropped subsets of the official ETT datasets; no additional preprocessing script is provided. MemCast uses a chronological 8:2 split: the first 80% of each dataset is used for memory accumulation, and the final 20% is used for testing. No separate validation split is used.

```bash
mkdir -p datasets
unzip /path/to/memcast_dataset.zip -d datasets/
```

### 4. Experience Accumulation (Memory Construction)
```
sh scripts/accumulation/build_memory.sh
```

### 5. Short-term Forecasting (e.g., NP)
```
sh scripts/short_term/NP.sh
```
### 6. Long-term Forecasting (e.g., ETTh)
```
sh scripts/long_term/ETTh.sh
```

### 7. Evaluation

After running forecasting, evaluate prediction quality using the uqmem scoring pipeline (LLM + heuristic trajectory selection):

**Short-term (EPF / NP, look_back=168, pred_window=24):**
```bash
bash scripts/evaluate/EPF.sh
```

**Long-term (ETTh1, look_back=96, pred_window=96):**
```bash
bash scripts/evaluate/ETTh.sh
```

Or run the evaluation scripts directly:
```bash
# EPF
python evaluate/EPF/evaluate_EPF_uqmem.py

# ETTh
python evaluate/ETTh/evaluate_ETT_uqmem.py
```

The evaluation reports **MSE** and **MAE** metrics for each attribute and prints a per-sample summary.

---

## 📊 Benchmark Results
**Full Results:**
![table1](assets/1-main-results.png)
Overall Performance: MemCast consistently achieves the best or second-best results across datasets, surpassing Time-LLM and TimeReasoner.

**Ablation Results:**
![table2](assets/2.png)

<p align="center">
  <img src="assets/3.png" width="45%">
  &emsp;
  <img src="assets/4.png" width="48%">
</p>
Ablation Study: Validation of memory components and dynamic adaptation strategy.




