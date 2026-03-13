# AAE5303 Assignment 2  
## Visual Odometry with ORB-SLAM3

---

# 1. Introduction

This project evaluates **monocular visual odometry** using **ORB-SLAM3** on the **HKisland dataset**.  
The objective is to estimate the camera trajectory from monocular images and compare the estimated trajectory with **RTK GPS ground truth**.

The performance is evaluated using:

- Absolute Trajectory Error (ATE)
- Relative Pose Error (RPE)
- Sim(3) trajectory alignment
- Completeness (association rate)

The evaluation is performed using the **evo trajectory evaluation toolkit**.

---

# 2. Experimental Setup

## Dataset

Dataset used:

HKisland_GNSS03.bag

Dataset characteristics:

| Parameter | Value |
|--------|--------|
| Sensor | Monocular Camera |
| Resolution | 2448 × 2048 |
| Frame Rate | 10 Hz |
| Duration | ~390 seconds |
| Ground Truth | RTK GPS |

---

## System Environment

| Component | Description |
|--------|--------|
| OS | Ubuntu (Virtual Machine) |
| SLAM System | ORB-SLAM3 |
| Evaluation Tool | evo |
| Programming Language | Python |

---

# 3. Methodology

The experiment follows these steps.

---

## Step 1: Run ORB-SLAM3

ORB-SLAM3 processes monocular images and estimates the camera trajectory.

Command used:

```bash
./Examples_old/ROS/ORB_SLAM3/Mono_Compressed \
Vocabulary/ORBvoc.txt \
Examples/Monocular/HKisland_fix.yaml

---

# 4. Results
**Absolute Trajectory Error (ATE)**.
Metric	Value
Max	16.552112 m
Mean	1.786148 m
Median	1.355826 m
Min	0.144398 m
RMSE	2.577282 m
Std	1.857972 m
SSE	7539.106151
Sim(3) Alignment

Trajectory alignment was performed using Sim(3) transformation.

Parameter	Value
Scale correction	1.8836
Translation	[-0.5667, 1.3446, 0.7044]
Association threshold	0.1 s
Completeness
Parameter	Value
Matched poses	540
Ground truth poses	1955
Completeness	27.62 %

Completeness calculation:

Completeness = 540 / 1955 × 100% = 27.62 %
Estimated Poses
Metric	Value
Estimated poses	1157

These poses are obtained from:

CameraTrajectory_sec.txt

