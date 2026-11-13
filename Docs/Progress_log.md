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
2. Made a fixed split of the AID dataset (aerial images, 30 scene classes such as airport, forest, parking; 600x600 pixels). Each HR image is shrunk 4x with bicubic interpolation to 150x150 (LR); the model learns LR -> HR. Images are drawn per class, so every split has all 30 classes, and the split is fixed (seed 0) so every run uses the same images:

| Split | Images | Per class | Used for |
|---|---|---|---|
| Train | 3,000 | 100 | training (random 256x256 HR crops) |
| Val | 60 | 2 | checked every 1,500 iterations during training to pick the best checkpoint |
| Test | 900 | 30 | scored once at the end; the final reported number |

   Val is small (60 images), so its PSNR is lower than test's (it happens to contain harder images) and noisier. Compare runs only on the same split.

## 11/11/2026:
1. Trained the MaIR baseline (E0, 75k iterations, batch 4, seed 10) and evaluated it on the **AID test set (900 images)**, x4, PSNR/SSIM on the Y (brightness) channel:

| Method | PSNR (dB) | SSIM |
|---|---|---|
| Bicubic | 28.71 | 0.7313 |
| MaIR-S (E0) | **30.64** | **0.8030** |

2. Wrote the window branch: 8x8 windows, 4 centre-out spirals + their reverses, Swin-style shifted windows.

## 12/11/2026:
1. Trained the window branch only (E-W, no stripe branch, batch 2, 75k iterations, seed 10).

## 13/11/2026:
1. Trained stripe + window branch fused by a plain sum (E1, batch 2, 75k iterations, seed 10). Results on the **AID val set (60 images)**, best checkpoint, x4, Y channel. E-W and E1 are not scored on the test set yet, so all three runs are compared on val here (E0's 30.32 on val is the same model as its 30.64 on test):

| Run | Branches | Batch | Params | PSNR (dB) | SSIM |
|---|---|---|---|---|---|
| E0 | stripe only (MaIR) | 4 | 1.37 M | **30.32** | **0.8013** |
| E-W | window only | 2 | 1.95 M | 30.25 | 0.7990 |
| E1 | stripe + window (sum) | 2 | 2.45 M | 30.27 | 0.7998 |

2. E1 is only +0.02 dB over E-W (within noise). Not yet a clean comparison with E0 (batch 2 = half the training crops); next: MaIR at batch 2 as the control.
