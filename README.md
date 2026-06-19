
# Adaptive Augmented Cubature Kalman Filter / Smoother for ECG Denoising

MATLAB implementation of an **adaptive augmented Cubature Kalman Filter (CKF)** and **Cubature Kalman Smoother (CKS)** for ECG denoising based on nonlinear Bayesian filtering.

This implementation follows the methodology proposed in the paper:

**Adaptive augmented cubature Kalman filter/smoother for ECG denoising**  
Hamed Danandeh Hesar, Amin Danandeh Hesar  
Biomedical Engineering Letters, Springer, 2024.

The algorithm models the ECG waveform using a **Gaussian mixture representation in the phase domain** and performs denoising using an **adaptive nonlinear state‑space model with cubature Kalman filtering and smoothing**.

---

# Overview

Electrocardiogram (ECG) signals are frequently corrupted by noise such as:

- muscle artifacts
- motion artifacts
- baseline drift
- sensor noise

Traditional linear filters are often insufficient for ECG denoising because ECG signals are **nonlinear and nonstationary**.

This project applies a **Bayesian nonlinear filtering framework** using:

- phase-domain ECG modeling  
- Gaussian mixture representation of ECG morphology  
- adaptive augmented cubature Kalman filtering  
- backward cubature Kalman smoothing  

The algorithm estimates the **clean ECG signal while adaptively updating noise statistics**.

---

# Algorithm Pipeline

The implemented system performs the following steps:

### 1. ECG Data Loading

The program loads ECG data from a MATLAB `.mat` file containing:

- `x` → ECG signal matrix  
- `fs` → sampling frequency (Hz)

The first channel of the signal is used for processing.

---

### 2. Noise Simulation

White Gaussian noise is added to the ECG signal:

```
SNR = 6 dB
```

This step simulates realistic noisy measurements.

---

### 3. R‑Peak Detection

R‑peaks are detected using the **Pan–Tompkins algorithm**.

```
[qrs_positions] = pantompkins_qrs(signal, fs)
```

Detected peaks define the cardiac cycles and are used to estimate ECG phase.

---

### 4. ECG Phase Calculation

Each heartbeat is mapped into a **phase domain representation**.

Phase is defined within the interval:

```
[-π , π]
```

This transformation converts the ECG signal into a periodic representation.

---

### 5. Mean ECG Extraction

The algorithm estimates:

- mean ECG waveform
- standard deviation of ECG amplitude

Both are computed over **phase bins**.

Additional smoothing is applied using **wavelet denoising**.

---

### 6. Gaussian Mixture ECG Modeling

The ECG morphology is approximated as a **sum of Gaussian kernels**:

```math
ECG(\theta) = \sum_{i=1}^{N} a_i \exp\left(-\frac{(\theta-\theta_i)^2}{2b_i^2}\right)
```

Where:

- \(a_i\) → amplitude  
- \(b_i\) → width  
- \( theta_i\) → center (phase position)

Parameters are estimated using **Particle Swarm Optimization (PSO)**.

Only the **strongest Gaussian kernels** are retained to build the ECG model.

---

# State-Space Model

The nonlinear state vector includes:

- ECG amplitude
- ECG phase
- instantaneous angular frequency
- Gaussian kernel parameters
- process noise augmentation terms

This **augmented state representation** allows the filter to model ECG morphology and signal dynamics simultaneously.

---

# Cubature Kalman Filter (CKF)

The **Cubature Kalman Filter** is used for nonlinear state estimation.

Key properties:

- third-order accurate nonlinear filtering
- deterministic cubature points
- no need for Jacobians
- more stable than EKF in strongly nonlinear systems

CKF estimates the hidden ECG state from noisy measurements.

---

# Adaptive Covariance Estimation

Measurement noise covariance **R** and process noise covariance **Q** are **adaptively updated** using:

- sliding window statistics
- innovation-based estimation
- forgetting factor

This adaptive mechanism improves robustness to varying noise conditions.

---

# Cubature Kalman Smoother (CKS)

After forward filtering, a **backward smoothing step** is applied.

The smoother uses future observations to refine state estimates:

```
Forward pass → CKF
Backward pass → CKS
```

This produces a more accurate ECG reconstruction.

---

# Input

The script requires a MATLAB `.mat` file containing:

```
x  → ECG signal matrix
fs → sampling frequency
```

When the program runs, MATLAB prompts the user to select the ECG file.

---

# Output

The algorithm generates:

### Denoised ECG (CKF)

Filtered signal estimate.

### Smoothed ECG (CKS)

Improved signal after backward smoothing.

### SNR Improvement Metrics

```
ACKF_SNR
ACKS_SNR
```

These values indicate the improvement achieved by the filtering and smoothing stages.

---

# Visualization

The program generates several plots.

### Figure 1
Noisy ECG with detected R‑peaks.

### Figure 2
Comparison of:

- original ECG
- noisy ECG
- Adaptive CKF output

### Figure 3
Comparison of:

- original ECG
- noisy ECG
- Adaptive CKS output

---

# Key Parameters

Important configurable parameters:

```
SNR                 % noise level
ecg_bins            % number of phase bins
MaxNumGaussian      % number of Gaussian kernels
window_size         % covariance adaptation window
forgetting_factor   % adaptive memory parameter
alpha, beta, kappa  % cubature sigma point parameters
```

---

# Required Functions

The following external functions must be available in the MATLAB path:

```
pantompkins_qrs.m
particleswarm
```

---

# MATLAB Toolboxes Required

- Signal Processing Toolbox  
- Wavelet Toolbox  
- Global Optimization Toolbox  

---

# Applications

This algorithm can be used in:

- ECG denoising research
- biomedical signal processing
- wearable health monitoring
- machine learning pipelines for ECG analysis

---

# Reference

H. Danandeh Hesar, A. Danandeh Hesar

**Adaptive augmented cubature Kalman filter/smoother for ECG denoising**

Biomedical Engineering Letters, Springer, 2024.

Paper link:

https://pmc.ncbi.nlm.nih.gov/articles/PMC11550304/

---

* نشان می‌دهد.
