---
layout: post
title: "Autonomous Exploration and Occupancy Grid Mapping with LIDAR"
date: 2025-12-10 11:30:00 +0200
categories: robotica-movil
---

## Introduction

In this practice, the objective was to design and implement an autonomous navigation algorithm that allows a mobile robot to **explore an industrial warehouse environment while building a probabilistic occupancy grid map** using LIDAR data.

The robot is equipped with a 360° laser sensor and different sources of localization information, ranging from perfect pose estimation to increasingly noisy odometry (`getOdom`, `getOdom2`, `getOdom3`).  
The exercise focuses on understanding how mapping can be performed when the robot’s pose is known, and how mapping quality deteriorates as localization uncertainty increases.

This practice combines **exploration**, **perception**, and **probabilistic mapping**, laying the foundations for more advanced techniques such as SLAM.

---

## Problem Overview

The task can be divided into two main components:

1. **Autonomous Exploration**  
   The robot must navigate the environment autonomously, covering as much area as possible without prior knowledge of the map.

2. **Occupancy Grid Mapping**  
   Using LIDAR measurements and the robot’s estimated pose, the robot incrementally builds a 2D probabilistic map that represents:
   - Occupied space (obstacles)
   - Free space
   - Unknown / unexplored areas

The map is visualized in real time using the `WebGUI.setUserMap()` function, which displays a grayscale image of size **1500 × 970 pixels**.

---

## Occupancy Grid Mapping Theory

An occupancy grid represents the environment as a discretized matrix where each cell stores the probability of being occupied.

- **Black (0)** → Occupied  
- **White (255)** → Free  
- **Gray (128)** → Unknown  

This representation was originally introduced by **Moravec and Elfes (1985)** and remains one of the most popular mapping methods due to its simplicity and robustness.

### Bayesian Update with Log-Odds

To combine sensor evidence over time, a probabilistic Bayesian model is used.
Instead of storing raw probabilities, the map stores **log-odds values**:

\[
l = \log\left(\frac{p}{1-p}\right)
\]

This allows incremental updates using simple additions and avoids numerical instability.

- Prior probability:  
  \( p_0 = 0.5 \Rightarrow l_0 = 0 \)

- Sensor model:
  - \( p_{occ} = 0.7 \) → obstacle detected
  - \( p_{free} = 0.3 \) → free space

Each new observation updates the map according to:

\[
l_t = l_{t-1} + (l_{sensor} - l_0)
\]

To prevent excessive certainty (inertia), the log-odds values are **saturated** within a fixed interval:

## LIDAR-Based Mapping Strategy

Each simulation cycle processes the 360 laser beams:

1. Angle Computation
Each laser index is converted into a global angle using the robot’s yaw.

2. Free Space Updating
If a laser beam returns inf, it means no obstacle was detected.
In this case, the ray is traced up to a maximum distance (INF_FREE_RANGE), marking intermediate cells as free.

3. Obstacle Detection
If a finite range is detected:

- The impact point is computed in Cartesian coordinates.

- The corresponding map cell is marked as occupied.

- A small inflation radius is applied to make obstacles thicker and more visible.

5. Bayesian Fusion
All updates are accumulated in log-odds form, combining evidence from multiple passes.

6. Visualization
The log-odds map is converted back to probabilities and thresholded to produce a grayscale occupancy grid.


## Exploration Algorithm: Random Exploration

To explore the environment, a random exploration strategy is used.
Although simple, this approach is sufficient to guarantee coverage in an enclosed environment.

## Obstacle Avoidance

Using the LIDAR data, three angular sectors are analyzed:

- Front sector

- Left sector

- Right sector

The minimum distance in each sector is extracted to guide decisions:

- If the front distance is safe → move forward

- If an obstacle is too close → stop and turn towards the side with more free space

This reactive control ensures collision-free motion while promoting exploration of new areas.

## Difficulties Encountered

1. Balancing Probabilistic Inertia
Choosing appropriate saturation values (L_MAX) was crucial.
Large values caused the map to become too rigid, while smaller values improved adaptability but increased noise sensitivity.

2. Ray Tracing Resolution
Selecting an adequate step size (STEP_FREE) was important to avoid gaps in free space detection.

3. Exploration Coverage
While random exploration is simple, it does not guarantee optimal or complete coverage, especially in large open areas.

## Results

The final implementation successfully:

- Autonomously explores the warehouse without collisions.

- Builds a probabilistic occupancy grid map in real time.

- Correctly distinguishes between free, occupied, and unknown space.

- Demonstrates clear degradation of map quality under noisy localization.

## Conclusion

This practice provides a clear introduction to probabilistic mapping with LIDAR and highlights the limitations of mapping with imperfect localization.
Despite using a simple exploration strategy, the robot is able to generate a meaningful representation of its environment.

The exercise serves as a solid stepping stone toward more advanced topics such as SLAM, frontier-based exploration, and map-based navigation.

## Laser Mapping video

[Ver el video en YouTube](https://youtu.be/TLfdZ0FgJJw)
