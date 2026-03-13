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

### Dataset

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

### System Environment

| Component | Description |
|--------|--------|
| OS | Ubuntu (Virtual Machine) |
| SLAM System | ORB-SLAM3 |
| Evaluation Tool | evo |
| Programming Language | Python |

---

# 3. Methodology

The experiment follows these steps:

### Step 1: Run ORB-SLAM3

ORB-SLAM3 processes monocular images and estimates the camera trajectory.

Command used:

```bash
./Examples_old/ROS/ORB_SLAM3/Mono_Compressed \
Vocabulary/ORBvoc.txt \
Examples/Monocular/HKisland_fix.yaml
This generates:
CameraTrajectory.txt
KeyFrameTrajectory.txt

Step 2: Extract Ground Truth
Ground truth trajectory is extracted from the ROS bag file using RTK GPS data.

Python script used:
import rosbag
import numpy as np

bag = rosbag.Bag('data/HKisland_GNSS03.bag')

rtk_data = []

for topic, msg, t in bag.read_messages(topics=['/dji_osdk_ros/rtk_position']):
    timestamp = msg.header.stamp.to_sec()
    lat, lon, alt = msg.latitude, msg.longitude, msg.altitude
    rtk_data.append([timestamp, lat, lon, alt])

bag.close()
The GPS coordinates are converted into local ENU coordinates and saved as:
ground_truth.txt

Step 3: Trajectory Evaluation

Trajectory alignment and error analysis are performed using the evo toolkit.

Command used:
evo_ape tum ground_truth.txt CameraTrajectory_sec.txt \
-va --align --correct_scale --t_max_diff 0.1 --plot --plot_mode xy
4. Results
Absolute Trajectory Error (ATE)
| Metric | Value          |
| ------ | -------------- |
| Max    | 16.552112 m    |
| Mean   | 1.786148 m     |
| Median | 1.355826 m     |
| Min    | 0.144398 m     |
| RMSE   | **2.577282 m** |
| Std    | 1.857972 m     |
| SSE    | 7539.106151    |

Sim(3) Alignment

Trajectory alignment was performed using Sim(3) transformation.
| Parameter             | Value                     |
| --------------------- | ------------------------- |
| Scale correction      | **1.8836**                |
| Translation           | [-0.5667, 1.3446, 0.7044] |
| Association threshold | **0.1 s**                 |
Completeness
Parameter
| Parameter          | Value       |
| ------------------ | ----------- |
| Matched poses      | 540         |
| Ground truth poses | 1955        |
| Completeness       | **27.62 %** |
Completeness = matched poses / ground truth poses × 100%
             = 540 / 1955 × 100%
             = 27.62 %
Estimated Poses
| Metric          | Value    |
| --------------- | -------- |
| Estimated poses | **1157** |
These poses are obtained from：
CameraTrajectory_sec.txt

5. Visualization
ORB-SLAM3 Runtime Visualization

This figure shows the ORB-SLAM3 runtime interface including the map viewer and feature tracking.
Trajectory Evaluation

The following figure shows the trajectory comparison between:

Ground truth trajectory

Estimated ORB-SLAM3 trajectory

ATE error distribution

Error along the trajectory

![ORB-SLAM3 Frame](frame.png)
![Trajectory Evaluation](trajectory_evaluation.png)
6. Difficulties Encountered

During the experiment, several technical difficulties were encountered.

Due to system compatibility issues on the host computer, pulling the required Docker image using PowerShell repeatedly failed. As a result, the SLAM environment could not be deployed directly on the host system.

To solve this issue, the entire experiment was eventually executed inside a Linux virtual machine, which allowed the Docker container and ORB-SLAM3 environment to run successfully.

Additionally, the Cursor development environment recommended in the assignment could not be used, and most operations had to be performed manually through the terminal.

Because of these environment limitations, the experiment workflow became somewhat disorganized, and the final trajectory overlap with the ground truth was lower than expected.

7. Recommendations for Improvement

Several improvements could potentially enhance the performance:

Increase ORB feature extraction number

Reduce FAST threshold for better feature detection

Use slower rosbag playback speed

Integrate IMU measurements

Run experiments in a more stable development environment

Future work could further optimize SLAM parameters and improve tracking robustness.

8. References

ORB-SLAM3
https://github.com/UZ-SLAMLab/ORB_SLAM3

https://github.com/uzh-rpg/evo
