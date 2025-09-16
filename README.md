Use modern log/normalization/resize/downsampling
Use superlet cache
Add visualization (introduce log and normalization and resize) 
Add docker + add requirements 
add readme 
add comments 
test with real data 
change exceptions 
add ss parameter control
add logger














# Superlet Transformation Toolkit

This repository is built upon the paper:
**"Time-frequency super-resolution with superlets"**
*Moca et al., Nature Communications (2021)*

and the official implementation by Harald Bârzan:
[Superlets (MIT License)](https://github.com/TransylvanianInstituteOfNeuroscience/Superlets.git).

---

## 📌 Purpose

The goal of this repository is to make the **Superlet Transform** easier to use for both **implementation** (e.g., batch processing, caching, parameter setting) and **visualization** (turning raw EEG/SEEG/LFP-like signals into time-frequency images).

---

## 🚀 Features

-   Read `.mat` files containing **1D time-series arrays** (amplitude vs. time).
-   Extract signal **windows** around known onset times and transform them into Superlet spectrograms.
-   Support for **multiple signals** by stacking 1D arrays before transformation.
-   **Cache Superlet objects** to avoid repeated initializations, which is critical for performance on large datasets.
-   Configure parameters such as:
    -   Frequency range and number of cycles (`c1`).
    -   Data options like **window size**, centered on an onset time.
    -   Padding and cropping settings to handle edge artifacts.

---

## ⚠️ Common Issues & Artifacts

When working with **short windows** (e.g., 2 seconds), artifacts can appear at the edges of the transformed images:

-   Circular “ripples” or lines appear on the **left and right borders**.
-   This is especially visible when the underlying signal has a slope or a strong low-frequency trend.

### 🧪 Things We Tried (and Why We Dropped Them)

1.  **Zero-padding**:
    -   Added zeros before and after the signal window.
    -   ❌ **Result**: Resulted in some improvement, but edge artifacts remained.

2.  **Subtracting the mean**:
    -   Removed the mean amplitude, which was helpful when the signal had a linear trend.
    -   ❌ **Result**: Slightly distorted the overall frequency content, and artifacts still remained.

3.  **Detrending (linear subtraction)**:
    -   Tried removing linear slopes from the signal.
    -   ❌ **Result**: This approach was unstable; it worked well for some signals but did not generalize effectively.

### ✅ What Actually Worked

-   **Padding the window with additional real signal context and then cropping the transformed output.**
-   This method produces the cleanest images with minimal artifacts.
-   We added parameters to make this process reproducible (`pad_length`, `crop_size`).

---

## 📦 Caching Strategy

-   Initializing a `Superlet` object for each window is computationally expensive.
-   Since all windows often share the **same size and frequency parameters**, we can **cache** the Superlet object.
-   This caching results in a significant speedup when processing many samples.

---