## 🧠 Core Concepts

The project is based on the following mathematical and computational steps:

| Stage                           | Description                                                                                                  |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| **1. Correspondence Selection** | Interactive manual selection of matching points between images using Matplotlib.                             |
| **2. Homography Estimation**    | Solves the linear system (Ah = 0) via SVD to compute the 3×3 projective transformation between image pairs.  |
| **3. Image Warping**            | Uses **backward warping** with bilinear interpolation to project one image into another’s coordinate system. |
| **4. Mosaicing**                | Determines global translation and bounding boxes to align all warped images into a shared coordinate space.  |
| **5. Blending**                 | Combines intensity values across overlapping regions to form a seamless panoramic image.                     |

---

## 🧩 Implemented Functions

| Function                                     | Purpose                                                                                    |
| -------------------------------------------- | ------------------------------------------------------------------------------------------ |
| `pick_and_save_correspondences(...)`         | Interactive correspondence selection and saving of coordinate pairs.                       |
| `computeH(points_im1, points_im2)`           | Computes the homography matrix using SVD from matched points.                              |
| `homography_by_sample(sample_path)`          | Loads saved correspondences and calculates the corresponding homography.                   |
| `warp(image, homography, output_shape=None)` | Performs backward warping with bilinear interpolation.                                     |
| `warp_full(image, homography)`               | Handles warped images extending beyond the original frame, adding translation adjustments. |
| `mosaic(images, homographies)`               | Aligns all warped images on a shared global canvas using computed homographies.            |
| `blend(images)`                              | Combines multiple warped images using maximum-intensity blending for seamless results.     |

---

## 🏗️ Project Structure

```
├── images/
│   ├── paris/
│   │   ├── paris_a.jpg
│   │   ├── paris_b.jpg
│   │   └── paris_c.jpg
│   ├── north_campus/
│   │   ├── left_1.jpg ... right_2.jpg
│   └── cmpe_building/
│       ├── left_1.jpg ... right_2.jpg
│
├── illustrations/
│   └── corresponding_points_example.jpg
│
├── src/
│   ├── correspondences.py
│   ├── homography.py
│   ├── warping.py
│   ├── mosaicing.py
│   └── blending.py
│
├── report/
│   └── main.tex
│
└── README.md
```

---

## 🖥️ How to Run

### 1. Pick Corresponding Points

```bash
python src/correspondences.py
```

* Opens an interactive window to select point correspondences between two images.
* Points are saved to `.npy` files for later homography computation.

### 2. Compute Homography

```bash
python src/homography.py
```

* Loads the saved correspondence file (e.g., `4_corresp.npy`).
* Computes and saves the homography matrix `H`.

### 3. Warp and Mosaic Images

```bash
python src/warping.py
python src/mosaicing.py
```

* Warps each image to the target coordinate frame using backward warping.
* Combines all warped images into a single mosaic using translation adjustment.

### 4. Blend the Final Panorama

```bash
python src/blending.py
```

* Merges aligned images using pixel-wise intensity blending.
* Produces the final panoramic result (e.g., `output/paris_panorama.jpg`).

---

## 📊 Image Sets

Three datasets were provided for evaluation:

1. **Paris (3 images)** — Small set used for initial homography testing and blending.
2. **North Campus (5 images)** — Medium-size dataset for testing translation and scaling stability.
3. **CMPE Building (5 images)** — Complex dataset used to test the full mosaicing pipeline.

Each dataset demonstrates the effect of homography accuracy, warping precision, and blending smoothness.

---

## 🧮 Mathematical Foundations

## 🧮 Mathematical Foundations

### Homography Estimation

Given correspondences **(xᵢ, yᵢ) ↔ (x′ᵢ, y′ᵢ)**, the homography satisfies:

```
[x′ᵢ  y′ᵢ  1]ᵀ  ≈  H · [xᵢ  yᵢ  1]ᵀ
```

The minimization problem is formulated as:

```
min ||A·h||   subject to   ||h|| = 1
```

This is solved using **Singular Value Decomposition (SVD)**, yielding:

```
H = reshape(v_min)
```

where `v_min` is the right singular vector corresponding to the smallest singular value of `A`.

---

### Backward Image Warping

For each pixel in the output image:

```
[x  y  1]ᵀ  =  H⁻¹ · [x′  y′  1]ᵀ
```

Color values are interpolated from the source using **bilinear interpolation**, ensuring smooth, hole-free warping.

---

### Global Translation for Mosaicing

All image corners are projected using their homographies to determine:

```
x_min, x_max, y_min, y_max
```

and a global translation **T** is constructed as:

```
T = | 1   0  −x_min |
    | 0   1  −y_min |
    | 0   0   1     |
```

---

## 🧩 Results

Sample panoramas generated:

| Dataset           | Preview                           |
| ----------------- | --------------------------------- |
| **Paris**         | ![Paris panorama](preview1.jpg)   |
| **North Campus**  | *see report for detailed results* |
| **CMPE Building** | *see report for detailed results* |

---

## ⚙️ Dependencies

* Python 3.10+
* NumPy
* OpenCV
* Matplotlib

Install all dependencies using:

```bash
pip install -r requirements.txt
```
---

## 🧾 License

© 2025 Amin Abu-Hilga. All rights reserved.
