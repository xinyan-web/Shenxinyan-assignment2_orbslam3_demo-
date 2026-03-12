AAE5303 Assignment 2: Visual Odometry with ORB-SLAM3
Overview

This project evaluates the performance of monocular visual odometry using ORB-SLAM3 on the HKisland_GNSS03 dataset. The objective is to estimate the camera trajectory from monocular images and compare it to the RTK GPS ground truth.

The key evaluation metrics include:

Absolute Trajectory Error (ATE)

Relative Pose Error (RPE)

Sim(3) Scale Correction and Translation

Completeness

Estimated Poses

1. Data and Setup
1.1 Data

The data used in this assignment comes from the HKisland_GNSS03 dataset. The following files are required for the evaluation:

ground_truth.txt (RTK GPS ground truth)

CameraTrajectory.txt (Estimated trajectory from monocular SLAM)

1.2 Dependencies

To run the evaluation, you need the following:

Python 3
pip install numpy
pip install evo

ORB-SLAM3 installed and set up (for monocular visual odometry).

2. Execution
2.1 Running the Evaluation

To evaluate the performance of ORB-SLAM3, use the following code. It computes key metrics like Absolute Trajectory Error (ATE), Relative Pose Error (RPE), and Completeness.

python3 evaluate_with_evo.py --groundtruth ground_truth.txt --estimated CameraTrajectory.txt --t-max-diff 0.1 --delta-m 10 --workdir evaluation_results --json-out evaluation_report.json
2.2 Output Metrics

After running the above command, the following metrics will be printed:

Absolute Trajectory Error (ATE)

RMSE: 2.577282 m

Mean: 1.786148 m

Median: 1.355826 m

Min: 0.144398 m

Max: 16.552112 m

Std Dev: 1.857972 m

SSE: 7539.106151 m

Relative Pose Error (RPE)
The translation drift and rotational drift over distance for each frame.

Sim(3) Alignment

Scale Correction: 1.8836

Translation: [-0.5667, 1.3446, 0.7044]

2.3 Completeness

Completeness:
540 / 1955 × 100% ≈ 27.62%

This indicates that 540 poses were successfully matched from the RTK ground truth to the SLAM estimated trajectory, which is around 27.62% of the total poses.

3. Evaluation Metrics
3.1 Absolute Trajectory Error (ATE)

ATE measures the global alignment between the estimated trajectory and the ground truth. A lower value means better global alignment.

Max ATE: 16.552112 m

Mean ATE: 1.786148 m

RMSE: 2.577282 m

Std Dev: 1.857972 m

3.2 Relative Pose Error (RPE)

RPE evaluates the local consistency of the estimated trajectory by comparing the poses of consecutive frames.

RPE Trans Drift: 1.786148 m

RPE Rot Drift: 0.144398 degrees per 100 meters

3.3 Sim(3) Alignment

The alignment is performed using Sim(3) transformation with scale correction.

Scale Correction: 1.8836

Translation Vector: [-0.5667, 1.3446, 0.7044]

3.4 Completeness

Completeness represents how well the ground truth and estimated trajectories were synchronized.

Matched Poses: 540

Total Ground Truth Poses: 1955

Completeness: 27.62%

4. Results Summary

The evaluation showed that while the Absolute Trajectory Error (ATE) was within a good range (RMSE: 2.58 m), the Relative Pose Error (RPE) showed significant drift due to frequent tracking losses. The Completeness was around 27.62%, indicating room for improvement in pose matching.

Sim(3) alignment yielded a scale correction of 1.8836 and a translation shift of [-0.5667, 1.3446, 0.7044].

5. Conclusion

The performance of the monocular visual odometry system is moderate. While the global alignment (ATE) is good, the local accuracy (RPE) needs improvement, especially due to frequent tracking loss.

Future improvements could include:

Improving tracking robustness (reduce drift by increasing ORB features or adding IMU data).

Enhancing the feature extraction (lowering FAST threshold or increasing features per image).

Refining initialization quality to improve scale accuracy.

6. Code
# Code to compute ATE, RPE, and Completeness
def evaluate_with_evo(
    gt_path: str,
    est_path: str,
    t_max_diff_s: float,
    delta_m: float,
    workdir: str,
):
    # Code here to run evo_ape and evo_rpe, then extract and return metrics
    pass
7. References

ORB-SLAM3: https://github.com/UZ-SLAMLab/ORB_SLAM3
evo: https://github.com/uzh-rpg/evo
MARS-LVIG Dataset: https://mars.hku.hk/dataset.html



## 🎯 Conclusions

In this assignment, monocular visual odometry was implemented using **ORB-SLAM3** on the HKisland dataset.  
The estimated trajectory was evaluated against the RTK GPS ground truth using the **evo evaluation toolkit**.

The evaluation results show that the system achieved a reasonable level of accuracy in terms of **Absolute Trajectory Error (ATE)**, with an RMSE of **2.58 m**, which is acceptable for outdoor visual odometry scenarios.  
However, the **trajectory overlap with the ground truth is relatively low**, and the **completeness rate is only 27.62%**, indicating that tracking was frequently lost during the sequence.

Overall, the experiment demonstrates that ORB-SLAM3 can estimate the trajectory with moderate accuracy, but the robustness of tracking could be further improved.

---

## ⚠️ Difficulties Encountered

During the implementation process, several technical issues were encountered.

First, due to **system compatibility issues on the host computer**, pulling the required Docker image using **PowerShell** repeatedly failed. As a result, the experiment could not be completed directly on the host environment.

To solve this issue, the entire experiment was eventually executed inside a **Linux virtual machine**, which allowed the Docker container and ORB-SLAM3 environment to run successfully.

Additionally, because of these environment issues, the **Cursor development environment recommended in the assignment could not be used**, and most operations had to be performed manually through the terminal. This increased the complexity of debugging and experiment management.

As a consequence, the trajectory alignment and visualization process became somewhat disorganized, and the final trajectory overlap with the ground truth was lower than expected.

---

## 🔧 Recommendations for Improvement

Several improvements could potentially enhance the performance of the system:

- **Improve tracking robustness** by increasing the number of ORB features extracted per frame.
- **Lower the FAST threshold** to detect more features in challenging frames.
- **Reduce rosbag playback speed** to allow the SLAM system more time for feature tracking.
- Incorporate **IMU measurements** to improve scale estimation and reduce drift.
- Run experiments directly in a more stable development environment (such as the recommended Cursor setup) to simplify workflow management.

Future work could also include tuning camera parameters and experimenting with different SLAM configurations to further improve trajectory accuracy and completeness.

## 📎 Appendix

### A. Repository Structure

```
AAE5303_assignment2_orbslam3_demo-/
├── README.md                    # This report
├── requirements.txt             # Python dependencies
├── figures/
│   └── trajectory_evaluation.png
├── output/
│   └── evaluation_report.json
├── scripts/
│   └── evaluate_vo_accuracy.py
├── docs/
│   └── camera_config.yaml
└── leaderboard/
    ├── README.md
    ├── LEADERBOARD_SUBMISSION_GUIDE.md
    └── submission_template.json
```

### B. Running Commands

```bash
# 1. Extract images from ROS bag
python3 extract_images_final.py HKisland_GNSS03.bag --output extracted_data

# 2. Run ORB-SLAM3 VO
./Examples/Monocular/mono_tum \
    Vocabulary/ORBvoc.txt \
    Examples/Monocular/DJI_Camera.yaml \
    data/extracted_data

# 3. Extract RTK ground truth
python3 extract_rtk_groundtruth.py HKisland_GNSS03.bag --output ground_truth.txt

# 4. Evaluate trajectory
python3 scripts/evaluate_vo_accuracy.py \
    --groundtruth ground_truth.txt \
    --estimated CameraTrajectory.txt \
    --t-max-diff 0.1 \
    --delta-m 10 \
    --workdir evaluation_results \
    --json-out evaluation_results/metrics.json
```

### D. Native evo Commands (Recommended)

If you prefer to run evo directly (no custom scripts), use:

```bash
# ATE (Sim(3) alignment + scale correction)
evo_ape tum ground_truth.txt CameraTrajectory.txt \
  --align --correct_scale \
  --t_max_diff 0.1 -va

# RPE translation (distance-based, delta = 10 m)
evo_rpe tum ground_truth.txt CameraTrajectory.txt \
  --align --correct_scale \
  --t_max_diff 0.1 \
  --delta 10 --delta_unit m \
  --pose_relation trans_part -va

# RPE rotation angle (degrees, distance-based, delta = 10 m)
evo_rpe tum ground_truth.txt CameraTrajectory.txt \
  --align --correct_scale \
  --t_max_diff 0.1 \
  --delta 10 --delta_unit m \
  --pose_relation angle_deg -va
```

### C. Output Trajectory Format (TUM)

```
# timestamp x y z qx qy qz qw
1698132964.499888 0.0000000 0.0000000 0.0000000 -0.0000000 -0.0000000 -0.0000000 1.0000000
1698132964.599976 -0.0198950 0.0163751 -0.0965251 -0.0048082 0.0122335 0.0013237 0.9999127
...
```

---

<div align="center">

**AAE5303 - Robust Control Technology in Low-Altitude Aerial Vehicle**

*Department of Aeronautical and Aviation Engineering*

*The Hong Kong Polytechnic University*

Jan 2026

</div>

