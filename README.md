# Variational Control for Guidance in Diffusion Models

Course project for **MVA — Generative Modeling for Images** (Justin Bec & Nicolas Beaujoin, March 2026).

## Overview

We implement and evaluate **NDTM** (Non-linear Diffusion Trajectory Matching), the guidance method
proposed in *Variational Control for Guidance in Diffusion Models* (Pandey et al., 2025), and compare
it against the **DPS** baseline (Diffusion Posterior Sampling, Chung et al., 2022) on image inverse
problems.

Instead of adding a guidance gradient to the sampling step (as DPS does), NDTM casts guided sampling
as a stochastic optimal control problem: at each denoising step, a control signal `u_t` is optimized
to minimize a local cost combining a **terminal cost** `F` (data consistency with the observation)
and a **KL regularization** that keeps the guided trajectory close to the unguided posterior. The
method requires no retraining of the diffusion model and handles linear, non-linear, and blind
inverse problems.

## Experiments

All experiments use a pre-trained diffusion model on **FFHQ** with DDIM sampling, on the following
inverse problems:

- **Gaussian deblurring** — terminal cost `F(x0) = ||k * x0 - y||^2`
- **Motion blur deblurring** — more complex, highly non-linear blur kernel
- **Random inpainting** — terminal cost `F(x0) = ||M x0 - y||^2` with a random mask
- **Large-area inpainting** — same cost with a mask hiding a large square region
- **Non-linear deblurring** — Gaussian blur followed by down-/upsampling

We also study a **guided initialization** trick (starting from a noisy version of the observation
rather than pure noise), used in the official implementation but not described in the paper.

### Findings

- NDTM performs slightly better than DPS on Gaussian and motion blur deblurring (PSNR over several
  test images), but uses ~1.5x more denoiser evaluations (150 vs. 100 in our setting).
- On random inpainting, both methods are comparable, with a slight advantage for DPS.
- NDTM performs poorly on large-area inpainting.
- On non-linear deblurring, both methods degrade but remain reasonable.

See the report for the full analysis.

## Repository contents

| File | Description |
|---|---|
| `project_ndtm.ipynb` | Main notebook: implementations of NDTM and DPS, and all experiments (cell outputs cleared) |
| `07_Beaujoin_Bec.pdf` | Project report |
| `GuidanceDiffModels.pdf` | Paper *Variational Control for Guidance in Diffusion Models* (Pandey et al., 2025) |
| `formulas_reference.pdf` | Reference sheet of the diffusion formulas used (DDPM, DDIM, DPS, NDTM) |

## References

- Kushagra Pandey, Farrin Marouf Felix Draxler, Theofanis Karaletsos, Stephan Mandt.
  *Variational Control for Guidance in Diffusion Models*. arXiv:2502.03686, 2025.
- Hyungjin Chung, Jeongsol Kim, Michael T. McCann, Marc L. Klasky, Jong Chul Ye.
  *Diffusion Posterior Sampling for General Noisy Inverse Problems*. arXiv:2209.14687, 2022.
- Jonathan Ho, Ajay Jain, Pieter Abbeel. *Denoising Diffusion Probabilistic Models*. NeurIPS, 2020.
- Jiaming Song, Chenlin Meng, Stefano Ermon. *Denoising Diffusion Implicit Models*. arXiv:2010.02502, 2020.
