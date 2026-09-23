# Sparse-view CT reconstruction with a U-Net

A small study on how much image quality a U-Net can recover when a CT scan is taken with fewer projection angles than usual.

## Why

Phase-contrast micro-CT can show the inside of a tissue biopsy in 3D without cutting it, at a resolution close to histology. The problem is scan time. It grows with the number of projection angles, and published scans of breast specimens have taken tens of hours at the exposure needed to see malignant structures. That is too slow for routine pathology work.

Fewer angles means a shorter scan. But filtered back projection (FBP) then leaves streak artifacts across the image, and fine details can be lost. So the question is whether the streaks can be reduced after the fact, and that is what I test here on simulated data.

## Scope

Everything here is simulated. The setup is deliberately simple: 2D slices, parallel-beam geometry, absorption contrast only, and a 10-epoch training run. It is not a study on real or clinical data.

## What the notebook does

1. Makes synthetic phantoms with a soft-tissue body, elliptical structures and small bright dots.
2. Simulates the scan with the Radon transform and Poisson photon-counting noise.
3. Reconstructs with FBP at 180 angles for the reference and 30 angles for the sparse input.
4. Builds 600 image pairs and splits them 80/10/10 into training, validation and test sets.
5. Trains a compact U-Net with MSE loss and Adam for 10 epochs, keeping the checkpoint with the lowest validation loss.
6. Scores FBP and the network against the same 180-angle reference with PSNR and SSIM.

## Results

The recorded test-set results are:

| Method | PSNR (dB) | SSIM |
|---|---:|---:|
| Sparse-view FBP | 27.09 | 0.555 |
| U-Net | 34.35 | 0.850 |

These values are from the held-out test set of 60 simulated images.

## Files

- `sparse_view_ct_unet.ipynb` — the workflow from phantom generation through testing.
- `outputs/` — generated figures, dataset, model weights and results.
- `requirements.txt` — required Python packages.

## Running it

```bash
pip install -r requirements.txt
jupyter notebook sparse_view_ct_unet.ipynb
```

It also runs in Google Colab. Run the notebook from top to bottom.

## Limitations

- The measurements are simulated and absorption-based, not experimental grating-interferometry phase-contrast data.
- The training target is a 180-angle FBP reconstruction, not the phantom itself. The experiment therefore measures how well a longer scan can be reproduced, rather than direct recovery of the true object.
- Reconstruction uses scikit-image and parallel-beam geometry. Laboratory micro-CT is typically cone-beam; a GPU toolkit such as ASTRA would be a natural next step.
- Only single slices are used.
- Real scans do not provide a clean reference in the same way, so a different training strategy would be needed for real data.

## Reproducibility

The phantom/noise generator uses `default_rng(0)`, and the train/validation/test split uses `default_rng(42)`.
