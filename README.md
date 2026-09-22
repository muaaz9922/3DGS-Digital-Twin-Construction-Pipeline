# 3DGS-Digital-Twin-Construction-Pipeline 🏗️📷

An end-to-end pipeline for generating highly accurate, photorealistic 3D digital twins of construction sites from uncalibrated drone imagery. This repository utilizes Structure-from-Motion (SfM) via COLMAP and state-of-the-art volumetric neural rendering via 3D Gaussian Splatting (3DGS). It outputs high-fidelity point clouds, automated fly-through videos, and geometric site analyses for CAD alignment.

## 📌 Overview

Traditional photogrammetry struggles with reflective surfaces, thin structures, and rendering speed. This project implements 3D Gaussian Splatting to synthesize a continuous 3D volume from sparse drone images. The resulting 4.7-million-point model is geometrically measurable, enabling automated tracking of excavation depth, material stockpiles, and structural deviations against architectural blueprints.

## 🚀 Key Features

* **Automated SfM Camera Extraction:** Seamless integration with COLMAP to extract 3D flight paths and thousands of visual anchor points.
* **Dynamic Lens Undistortion:** Built-in CLI operations to flatten curved drone imagery and strictly enforce the `PINHOLE` camera model required for neural training.
* **Volumetric Neural Rendering (3DGS):** Custom CUDA rasterization kernels trained to output highly detailed, continuous 3D scenes (achieving ~28.46 dB PSNR).
* **Automated Video Compilation:** Headless rendering of trained Gaussians mapped back to original camera trajectories and stitched into an MP4 fly-through using OpenCV.
* **Spatial Analysis:** Open3D integration to denoise the resulting point cloud, extract Axis-Aligned Bounding Boxes (AABB), compute site volumes, and simulate Iterative Closest Point (ICP) alignment.

## 🏗️ Pipeline Architecture

The workflow is broken down into five automated execution phases:

1. **Phase 1: Data Preparation**  
   Ingestion of uncalibrated drone captures and generation of synthetic CAD baseline benchmarks.
2. **Phase 2: Structure-from-Motion (COLMAP)**  
   Feature extraction, matching, and triangulation to establish a sparse 3D geometry and register precise camera poses.
3. **Phase 3: Image Undistortion & 3DGS Training**  
   Converting flight data to a strict PINHOLE coordinate space, followed by 7,000 optimization iterations to train the 3D Gaussian field.
4. **Phase 4: Render & Video Synthesis**  
   Rendering 2D frames from the optimized 3D mathematical model and compiling an overview `.mp4` video.
5. **Phase 5: Geometric Analysis (Open3D)**  
   Filtering atmospheric noise, establishing site bounding volumes, and cross-referencing cubic dimensions against the Phase 1 CAD blueprint.

## 💻 Technology Stack

| Component | Technology | Purpose |
| :--- | :--- | :--- |
| **Environment** | Python 3.10+, Kaggle (Tesla T4 GPU) | Compute environment and execution context |
| **Computer Vision** | OpenCV | Image processing and video synthesis |
| **Structure-from-Motion** | COLMAP, PyColmap | Sparse point cloud generation and camera tracking |
| **Neural Rendering** | 3D Gaussian Splatting, PyTorch | Volumetric training and photorealistic scene synthesis |
| **3D Data Processing**| Open3D | Point cloud filtering, bounding box, and spatial analysis |

## ⚙️ Installation & Requirements

This pipeline was developed and optimized for GPU-accelerated cloud environments (e.g., Kaggle, Google Colab) to bypass complex local CUDA compilation.

**Core Dependencies:**
```bash
# Install COLMAP command-line interface
sudo apt-get update -y
sudo apt-get install colmap -y

# Install 3DGS Custom Submodules
pip install -q plyfile tqdm open3d
pip install --force-reinstall --no-cache-dir ./submodules/diff-gaussian-rasterization
pip install --force-reinstall --no-cache-dir ./submodules/simple-knn
