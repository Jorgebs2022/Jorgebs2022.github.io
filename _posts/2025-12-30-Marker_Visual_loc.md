---
layout: post
title: "Marker-Based Visual Localization with AprilTags and Odometry Fusion"
date: 2025-12-30 11:30:00 +0200
categories: robotica-movil
---

## Introduction

In this practice, the objective was to design and implement a localization algorithm that allows a mobile robot to estimate its 2D pose (x, y, yaw) in a known environment by detecting AprilTags placed at known positions.

The AprilTags act as absolute landmarks (global reference), while odometry provides continuous but noisy motion estimation. The final solution combines both sources:

Vision fixes (AprilTag-based pose estimates) are used to “re-anchor” the robot to the world.

Odometry propagation is used when no tags are visible.

Outlier rejection prevents unstable updates from distant/noisy detections.

As in the simulator visualization:

- Green robot → ground-truth pose

- Blue robot → odometry (noisy)

- Red robot → estimated pose (our algorithm)

---

## Problem Overview

The task can be divided into three main components:

1. **Reactive Navigation** (Exploration / Motion)
The robot moves autonomously while avoiding obstacles using LIDAR-based reactive control.

2. **Visual Landmark Detection**
The robot detects AprilTags in the camera image and extracts the tag ID and corner locations.

3. **Pose Estimation + Fusion**
Using PnP, the robot estimates its pose in the global map and fuses it with odometry, rejecting unstable fixes.

---

## Visual Localization Theory

AprilTags as Global Landmarks

AprilTags are fiducial markers that can be reliably detected and uniquely identified. Given:

- The 2D pixel coordinates of the tag corners in the image

- The 3D corner coordinates in the tag reference frame (known size)

- The camera intrinsic parameters

we can solve the Perspective-n-Point (PnP) problem to obtain the tag pose relative to the camera.


### Implemented Algorithm

### Initial Configuration

We configure:

- AprilTag detector

- Motion state machine parameters

- Fusion variables for odometry anchoring

- Thresholds for rejecting visual outliers

The logic behind these fusion variables is:

- world_ref: last accepted absolute pose from vision (world coordinates)

- odom_ref: odometry pose when the last vision fix was taken

- have_fix: whether we already have a valid world anchor

- outlier thresholds prevent updating the anchor with unstable detections

### Reactive Motion with LIDAR

The robot navigates autonomously with a simple state machine:

- FORWARD: move forward with small random angular noise for exploration

- TURN: stop and rotate when an obstacle is detected in front

### AprilTag Detection and Debug Visualization

Each cycle we read the camera image, convert to grayscale, and detect tags

image = HAL.getImage()
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
results = detector.detect(gray)

For debugging, we draw:

- bounding box

- center point

- tag family label

This is useful to visually validate detection stability and corner quality, especially at long distances.



### Pose Estimation with PnP

Camera Intrinsics (Default Model)

The exercise provides a default pinhole approximation:
f = w_img
cx, cy = w_img / 2.0, h / 2.0
K = np.array([[f, 0, cx],
              [0, f, cy],
              [0, 0,  1]], dtype=np.float64)
dist = np.zeros((4, 1), dtype=np.float64)

#### Tag 3D Corner Model

The black border size is 0.24 × 0.24 m, so corners are defined around the tag center

#### Choosing the Best Tag

When multiple tags are visible, we select the one with the smallest estimated distance (more stable corners and less noise)

### Transformations Between Frames

To compute the robot pose in the world we chain homogeneous transforms:

- **world2camera = T_world_tag @ tag2camera_matrix @ cam2robot**

1) camera → tag from PnP

camera2tag[:3, :3] = cv2.Rodrigues(rvec)[0]
camera2tag[:3, 3]  = tvec.ravel()


Then invert:

tag2camera_matrix = np.linalg.inv(camera2tag)

2) Axis correction for AprilTag/OpenCV conventions

Two fixed rotations are applied:

−90° around X

−90° around Z (implemented via the provided matrices)

tag2camera_matrix = z_axis_rotation_matrix @ x_axis_rotation_matrix @ tag2camera_matrix

3) world → tag from YAML

Using the tag yaw and position:

T_world_tag = np.array([
    [ c, -s,  0,  x],
    [ s,  c,  0,  y],
    [ 0,  0,  1,  z],
    [ 0,  0,  0,  1]
], dtype=np.float64)

4) camera → robot (camera extrinsics)

The camera is not located at the robot origin, so we add an offset:

cam2robot = np.array([[1,0,0,0.069],
                      [0,1,0,-0.047],
                      [0,0,1,0.107],
                      [0,0,0,1]], dtype=np.float64)


A 90° yaw alignment correction is applied:

cam2robot = T_rot @ cam2robot

5) Final pose in the world
world2camera = T_world_tag @ tag2camera_matrix @ cam2robot
x_est = world2camera[0, 3]
y_est = world2camera[1, 3]
yaw_est = atan2(world2camera[1, 0], world2camera[0, 0])

### Odometry Fusion and Outlier Rejection

AprilTag fixes can become unreliable at long distances (few pixels, noisy corners).
To avoid corrupting the global pose, we reject updates that produce large jumps when the tag is far.

### Odometry propagation when no tags are visible

If no tags are detected, we propagate from the last accepted world anchor using odometry deltas. This keeps the estimate continuous even when tags disappear.

---

## Difficulties Encountered

1. **Unstable visual fixes at long distance**
Distant tags occupy few pixels and corner noise causes large errors.
This was solved by selecting the closest tag and rejecting large jumps.

2. **Frame convention mismatches (OpenCV vs AprilTag vs robot)**
Correct axis alignment required applying fixed rotations to the inverted PnP transform.

---

## Results

The final implementation achieves:

- Smooth autonomous motion with LIDAR obstacle avoidance

- Robust AprilTag detection and visualization

- Reliable pose estimation when tags are visible

- Continuous pose tracking using odometry when tags are not visible

- Improved stability thanks to outlier rejection, preventing large position jumps

---

## Conclusion

This practice demonstrates a complete pipeline for marker-based visual localization:

- AprilTags provide absolute global anchoring

- PnP provides the relative pose between camera and tag

- Homogeneous transforms convert the estimate into world coordinates

- Odometry ensures continuity between visual observations

- Outlier rejection improves robustness in real scenarios

This approach provides a strong foundation for more advanced localization techniques such as visual-inertial odometry and SLAM, where similar fusion concepts are applied but using probabilistic filters.

---

## Marker-Based Visual Localization

[Ver el video en YouTube](https://youtu.be/XEBCPtInx88)
