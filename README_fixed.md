
# AAE5303 ASSIGNMENT 2  
## VISUAL ODOMETRY WITH ORB-SLAM3

---

# 1 INTRODUCTION

This project evaluates **monocular visual odometry** using **ORB-SLAM3** on the **HKisland dataset**.

The objective is to estimate the camera trajectory from monocular images and compare the estimated trajectory with **RTK GPS ground truth**.

The performance is evaluated using:

- Absolute Trajectory Error (ATE)
- Sim(3) trajectory alignment
- Completeness (association rate)

The evaluation is performed using the **evo trajectory evaluation toolkit**.

---

# 2 EXPERIMENTAL SETUP

## DATASET

Dataset used:

HKisland_GNSS03.bag

Dataset characteristics:

| PARAMETER | VALUE |
|-----------|-------|
| SENSOR | Monocular Camera |
| RESOLUTION | 2448 × 2048 |
| FRAME RATE | 10 Hz |
| DURATION | ~390 seconds |
| GROUND TRUTH | RTK GPS |

---

## SYSTEM ENVIRONMENT

| COMPONENT | DESCRIPTION |
|-----------|-------------|
| OS | Ubuntu (Virtual Machine) |
| SLAM SYSTEM | ORB-SLAM3 |
| EVALUATION TOOL | evo |
| LANGUAGE | Python |

---

# 3 METHODOLOGY

The experiment follows three main steps.

---

## STEP 1 RUN ORB-SLAM3

ORB-SLAM3 processes monocular images and estimates the camera trajectory.

Command used:

```bash
./Examples_old/ROS/ORB_SLAM3/Mono_Compressed Vocabulary/ORBvoc.txt Examples/Monocular/HKisland_fix.yaml
```

This generates:

```
CameraTrajectory.txt
KeyFrameTrajectory.txt
```

---

## STEP 2 EXTRACT GROUND TRUTH

Ground truth trajectory is extracted from the ROS bag file using RTK GPS data.

Python script:

```python
import rosbag
import numpy as np

bag = rosbag.Bag('data/HKisland_GNSS03.bag')

rtk_data = []

for topic, msg, t in bag.read_messages(topics=['/dji_osdk_ros/rtk_position']):
    timestamp = msg.header.stamp.to_sec()
    lat, lon, alt = msg.latitude, msg.longitude, msg.altitude
    rtk_data.append([timestamp, lat, lon, alt])

bag.close()
```

The GPS coordinates are converted into local ENU coordinates and saved as:

```
ground_truth.txt
```

---

## STEP 3 TRAJECTORY EVALUATION

Trajectory alignment and error analysis are performed using **evo**.

Command:

```bash
evo_ape tum ground_truth.txt CameraTrajectory_sec.txt -va --align --correct_scale --t_max_diff 0.1 --plot --plot_mode xy
```

---

# 4 RESULTS

## ABSOLUTE TRAJECTORY ERROR (ATE)

| METRIC | VALUE |
|------|------|
| MAX | 16.552112 m |
| MEAN | 1.786148 m |
| MEDIAN | 1.355826 m |
| MIN | 0.144398 m |
| RMSE | **2.577282 m** |
| STD | 1.857972 m |
| SSE | 7539.106151 |
<img width="722" height="635" alt="image" src="https://github.com/user-attachments/assets/0087a1a3-f391-481d-a8a0-29cf54052232" />


---

## SIM(3) ALIGNMENT

| PARAMETER | VALUE |
|-----------|-------|
| SCALE CORRECTION | **1.8836** |
| TRANSLATION | [-0.5667, 1.3446, 0.7044] |
| ASSOCIATION THRESHOLD | **0.1 s** |

---

## COMPLETENESS

| PARAMETER | VALUE |
|-----------|-------|
| MATCHED POSES | 540 |
| GROUND TRUTH POSES | 1955 |
| COMPLETENESS | **27.62 %** |

Calculation:

Completeness = 540 / 1955 × 100% = 27.62 %

---

## ESTIMATED POSES

| METRIC | VALUE |
|-------|------|
| ESTIMATED POSES | **1157** |

Trajectory file:

CameraTrajectory_sec.txt

---

# 5 VISUALIZATION

## ORB-SLAM3 RUNTIME

This figure shows the ORB-SLAM3 runtime interface including the map viewer and feature tracking.

![ORB-SLAM3 Frame](frame.png)

---

## TRAJECTORY EVALUATION

The following figure shows the comparison between the estimated trajectory and the ground truth trajectory.

![Trajectory Evaluation](trajectory_evaluation.png)

---

# 6 DIFFICULTIES ENCOUNTERED

During the experiment several technical issues were encountered.

Due to **system compatibility problems on the host computer**, pulling the Docker image using **PowerShell** repeatedly failed. As a result, the SLAM environment could not be deployed directly on the host system.

To resolve this problem, the entire experiment was executed inside a **Linux virtual machine**, which allowed Docker and ORB-SLAM3 to run successfully.

Additionally, the **Cursor development environment recommended in the assignment could not be used**, so most operations were performed manually through the terminal.

Because of these environment limitations, the experiment workflow became somewhat disorganized and the trajectory overlap with ground truth was lower than expected.

---

# 7 RECOMMENDATIONS FOR IMPROVEMENT

Several improvements could enhance the performance:

- Increase ORB feature extraction number  
- Reduce FAST threshold for better feature detection  
- Use slower rosbag playback speed  
- Integrate IMU measurements  
- Use a more stable development environment

Future work could further improve tracking robustness and trajectory accuracy.

---

# 8 REFERENCES

ORB-SLAM3  
https://github.com/UZ-SLAMLab/ORB_SLAM3
