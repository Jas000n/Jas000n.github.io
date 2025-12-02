---
title: Rethinking 3D Reconsctruction
author: Jason
date: 2025-12-02
category: Jekyll
layout: post
cover:
---

### 1. SFM(Structure From Motion)

1. Feature Extraction
   1. Find keypoints:
      1. Scale-Space Construction: blur the image and generate a set of images -> Result: At this point, the computer possesses a "comprehensive set" of images ranging from clear to blurry, and from large to small.
      2. Keypoint Localization: difference of gaussian, extreme detection -> Results: **Blobs** or **Corners**.
2. Feature Matching
3. Sparse Reconstruction
   ![alt text](../assets/pics/reconstruction_3d/sfm.png)

### 2. NeRF (Neural Radiance Fields)

![NeRF pipeline](../assets/pics/reconstruction_3d/nerf_pipeline.jpg)  
![NeRF rendering](../assets/pics/reconstruction_3d/nerf.gif)

1. **Ray Sampling**  
   For every pixel, NeRF casts a ray into the scene and samples a set of 3D points along the ray.

2. **A Model to Predict Density and Color**  
   The core of NeRF is an MLP that maps each sampled 3D point (plus viewing direction) to:

   - σ (density)
   - RGB color

   This allows NeRF to represent both geometry and appearance.

3. **Volume Rendering**  
   NeRF integrates colors and densities using a volume rendering equation to compute the final pixel color.  
   Points with higher density occlude points behind them, and contributions are accumulated along the ray.

### 3. Gaussian

### 4. LSS

1. **Lift**  
    Each pixel feature corresponds to a 3D ray.  
    LSS predicts a probability distribution over depth bins for every pixel.  
    Then each pixel is lifted into multiple possible 3D positions using camera intrinsics/extrinsics.
   ![lift](../assets/pics/reconstruction_3d/lift.png)
2. **Splat**  
   All lifted 3D features are projected (“splatted”) onto a 2D BEV(bird eye's view) ground.  
   Features falling into the same BEV cell are aggregated, producing a BEV feature map.

3. **Shoot**  
    Once BEV features are obtained, it could be feed into diffrent heads for different downstream tasks:  
    - 3D object detection  
    - BEV segmentation  
    - Road layout prediction, etc.
   ![lss](../assets/pics/reconstruction_3d/lss.png)  
   ![bev](../assets/pics/reconstruction_3d/bev_pic.png)

### 5. Occupancy Prediction

![alt text](../assets/pics/reconstruction_3d/voxelformer.png)
![occ_bev](../assets/pics/reconstruction_3d/surrounding_env.png)

### 6. End2End: VGGT

![alt text](../assets/pics/reconstruction_3d/vggt_pipeline.png)
![alt text](../assets/pics/reconstruction_3d/dpt.png)
