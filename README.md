# Sparse Image Reconstruction

A computational imaging project for reconstructing images from sparse-view tomographic measurements using a U-Net.

## Overview

This project simulates a sparse-view CT reconstruction problem and evaluates whether a convolutional neural network can improve the reconstruction obtained from a limited number of projection angles.

The workflow is entirely computational and uses synthetic phantoms rather than experimental or patient data.

## Method

The project follows these main steps:

1. Generate synthetic 2D phantoms with randomly placed elliptical structures and small high-intensity features.
2. Simulate tomographic measurements using the Radon transform.
3. Model photon-counting noise with a Poisson distribution.
4. Reconstruct images using filtered back projection (FBP).
5. Generate:
   - a 180-view reconstruction used as the reference;
   - a 30-view reconstruction representing the sparse-view input.
6. Build a dataset of 600 simulated images.
7. Split the dataset into 80% training, 10% validation, and 10% test sets.
8. Train a U-Net to map sparse-view FBP reconstructions to the 180-view reference reconstructions.
9. Evaluate the FBP and U-Net reconstructions using PSNR and SSIM.

## Model

The reconstruction network is a compact U-Net implemented in PyTorch.

The network contains:

- an initial convolutional block;
- two downsampling stages;
- a bottleneck;
- two upsampling stages with skip connections;
- a final 1×1 convolution producing the reconstructed image.

The model is trained with mean squared error (MSE) loss and the Adam optimizer.

## Results

The final test-set results recorded in the notebook are:

| Method | PSNR | SSIM |
|---|---:|---:|
| Sparse-view FBP | 27.09 | 0.555 |
| U-Net | 34.35 | 0.850 |

Under this simulated setup, the U-Net reconstruction has higher PSNR and SSIM than the sparse-view FBP reconstruction.

## Repository Contents

- `Untitled1.ipynb` — complete computational workflow, including simulation, dataset generation, U-Net training, evaluation, and visualization.

## Limitations

This project is a proof-of-concept computational study. The current implementation uses synthetic phantoms and simulated attenuation-based tomographic measurements. It does not use experimental X-ray data, clinical data, or grating-interferometry phase-contrast measurements.

A natural next step would be to investigate the approach with more realistic X-ray imaging data and, where appropriate, phase-contrast CT data.

## Technologies

- Python
- NumPy
- SciPy / scikit-image
- Matplotlib
- PyTorch

## Reproducibility

The notebook uses fixed random seeds for the phantom generation and train/validation/test split. The complete workflow is contained in `Untitled1.ipynb`.
