# This is the progress log for Stripe Meets Window: Merging Stripe and Windowed Mamba Scans with Learned Routing for Local and Global Context in Remote Sensing Super-Resolution

# Log:
## 4/11/2026:
1. Downloaded mamba on wsl and installed it, and downloaded EAMamba and installed it as well. Setup the environment as well. Downloaded MambaIR as a reference as well.
2. Downloaded a dummy dataset cifar, and tried to train and infer EAMamba on it to make sure everything is in order
3. Downloaded AIDs dataset and LoveDA-Val and inspected them
4. Read several similar paper (literature Review)
    - [MambaIR: A Simple Baseline for Image Restoration with State-Space Model](https://www.ecva.net/papers/eccv_2024/papers_ECCV/papers/02740.pdf). ECCV 2024.
    - Lin et al. [EAMamba: Efficient All-Around Vision State Space Model for Image Restoration](https://openaccess.thecvf.com/content/ICCV2025/papers/Lin_EAMamba_Efficient_All-Around_Vision_State_Space_Model_for_Image_Restoration_ICCV_2025_paper.pdf). ICCV 2025.
    - Y.-K. Hsieh et al. [Partial Ring Scan: Revisiting Scan Order in Vision State Space Models](https://arxiv.org/abs/2602.04170). ICML 2026.
    - S. Chen, L. Zhang, L. Zhang. [HSRMamba: Contextual Spatial-Spectral State Space Model for Single Hyperspectral Image Super-Resolution](https://arxiv.org/abs/2501.18500). IJCAI 2025 (arXiv:2501.18500).

## 10/11/2026:
1. Moved to a new laptop (RTX 4050, 6 GB), rebuilt the environment, and downloaded the official MaIR code as the baseline.
2. Made a fixed AID split (3,000 train / 60 val / 900 test, bicubic x4, native 600x600).

## 11/11/2026:
1. Trained the MaIR baseline (E0, 75k iterations, batch 4): AID test **30.64 dB / 0.8030** vs bicubic 28.71 / 0.7313.
2. Wrote the window branch: 8x8 windows, 4 centre-out spirals + their reverses, Swin-style shifted windows.

## 12/11/2026:
1. Window branch only (E-W, batch 2): AID val **30.25 dB / 0.7990** (E0: 30.32 / 0.8013).

## 13/11/2026:
1. Stripe + window branch, plain sum (E1, batch 2): AID val **30.27 dB / 0.7998**, only +0.02 dB over window-only (within noise).
2. Not yet a clean comparison with E0 (batch 2 = half the training crops); next: MaIR at batch 2 as the control.
