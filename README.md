# SLAM-Lite: Visual Mapping and Path Estimation Using the GrapeSLAM UAV Dataset
### AAI 521 - Group 7
### Contributors: Christopher Mendoza, Payal Patel, Tommy Poole

## Project Overview
This project develops a lightweight SLAM-Lite visual mapping pipeline for drone navigation using monocular video. The goal is to reconstruct 3D structure and estimate the UAV's trajectory using camera data from the GrapeSLAM research dataset. Our approach focuses on feature detection, optical flow, pose estimation, and trajectory refinement using GPS-based ground truth for evaluation.


This project explores how far a simplified SLAM system can go in visually complex environments such as vineyards, where texture, lighting, and flight stability can vary. We benchmark monocular visual odometry (VO) performance and implement alignment techniques to reduce drift and compare results across multiple flights.

## Objectives
- Build a lightweight monocular SLAM pipeline suitable for real time drone navigation.
- Evaluate how environmental factors (brightness, blur, GPS stability, etc.) influence VO quality.
- Quantitatively compare unaligned and aligned VO trajectories against ground truth GPS.
- Reconstruct 3D structure and estimate trajectory.
- Demonstrate the feasibility of a "SLAM-Lite" system in various environmental conditions.

## Dataset
GrapeSLAM UAV Dataset: https://zenodo.org/records/14658376

The dataset contains 49 files, including 24 RGB video recordings captured by a UAV flying over a vineyard in Spain. Each video represents a separate flight under different lighting and environmental conditions. The dataset totals to around 40 GB and includes metadata files with details such as flight height, roll, pitch, speed, and distance.
This raw dataset is ideal for evaluating VO robustness.

## Methodology
#### 1. ORB-Based Visual Odometry
The VO pipeline uses ORB keypoint detection and BRIEF descriptors, followed by brute-force Hamming matching. Successive frame correspondences are used to estimate the essential matrix, recover rotation and translation, and form a chained global trajectory.
#### 2. Refinement and Drift Reduction
To improve stability and reduce noise:
- Lowe's Ratio Test filters ambiguous matches
- RANSAC removes outliers during pose estimation
- Cheirality checks validate triangulated points
- Median depth-ratio normalization stabilizes scale
- Exponential Moving Average and Savitzky-Golay filters smooth the trajectory
#### 3. Global Scale Correction
VO trajectories were aligned to GPS timestamps and converted into local ENU coordinates.
Comparing VO step lengths to GPS step lengths produced a robust global scale factor, enabling metric corrected visual paths.
#### 4. 2D and 3D Alignment
To address global rotation and translation drift, a 2D Procrustes alignment was applied, optimizing scale, rotation, and translation.
A full 3D Procrustes alignment produced almost identical results, confirming internal geometric consistency of the VO system.

## Results
| Metric               | Pre-Alignment (GPS-Scaled) | Post-Alignment (2D) | Post-Alignment (3D) |
|----------------------|-----------------------------|----------------------|----------------------|
| Mean Error           | 7.461 m                     | 0.360 m              | 0.361 m              |
| Median Error         | 7.065 m                     | 0.265 m              | 0.262 m              |
| RMSE                 | 8.885 m                     | 0.445 m              | 0.445 m              |
| Maximum Error        | 16.109 m                    | 1.332 m              | 1.340 m              |
| Drift per 100 m      | 157.1 m                     | 2.08 m               | 1.79 m               |

#### Summary
- Raw monocular VO displayed expected drift due to scale ambiguity.
- Refinement techniques reduced local noise but not global drift.
- GPS based scale correction provided metric accuracy.
- 2D and 3D Procrustes alignment produced sub-meter precision.
- The near identical 2D and 3D results suggest a strong internal consistency in motion reconstruction.


