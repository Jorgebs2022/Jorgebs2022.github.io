---
layout: post
title: "Auto Parking"
date: 2025-11-07 10:50:00 +0200
categories: robotica-movil
---

## Introduction

In this practice, the goal was to develop a navigation and control algorithm for an autonomous vehicle capable of parking itself.
The robot starts moving along a row of parked cars, identifies a free parking space using laser sensors, and executes a sequence of maneuvers to park accurately within that space.

This exercise combines perception (detecting free spots) and control (executing the parking maneuvers) using data from three laser sensors—front, right, and back.

## Plan Algorithm Overview

The implementation can be divided into two main phases: searching for the parking spot and executing the parking maneuver.
Throughout both phases, the algorithm makes extensive use of the laser data to perceive the environment and make control decisions in real time.

1. **Environment Detection**:
    Three lasers are used:

    - Front laser: detects cars or walls in front of the vehicle.

    - Right laser: detects parked cars and free spaces along the right side.

    - Back laser: used when reversing to ensure the vehicle doesn’t collide.

    To simplify detection, a rectangular detection zone is defined relative to the car’s coordinate system. This zone represents the area next to the car where a free space should appear.

    For every simulation cycle, the algorithm checks if there are valid laser readings inside this rectangle.
    If none are found, it means that the area is empty—indicating a potential parking spot.

2. **Following the Row of Cars**:
    To follow the line of parked cars while keeping a consistent lateral distance, I implemented a proportional control law (P-controller) through the keep_distance() function.
    This controller divides the right-side laser readings into two halves (front and back) and calculates the difference in distances between them.

    If the front readings are larger than the back ones, it means the car is turning away from the line of cars, and the control adjusts the angular velocity w to correct the orientation.

    Simultaneously, a secondary control keeps the car at a fixed distance (e.g., 2 meters) from the side cars, avoiding both collisions and excessive separation.

3. **Spot Detection and Decision Making**:
    Once the system detects an empty rectangular region with no nearby laser returns, the algorithm assumes that a free parking space has been found.
    To confirm the detection, the algorithm counts several consecutive cycles (SEEN_TICKS) in which the spot remains unoccupied—this avoids false positives due to sensor noise or temporary gaps.

    When the conditions are met, the car transitions to the parking maneuver phase.

4. **Parking Maneuver**:
   The parking maneuver is handled as a state machine, where each step of the process is represented by a distinct state.
    This structure makes it easier to control complex sequences of movements, such as combining backward and rotational motion.

    The main states are:

    | State | Description |
    |--------|-------------|
    | `LOOK_FOR_SIT` | Follow the row of cars and look for a free spot. |
    | `BACK_RIGHT` | Start reversing while turning right to align with the space. |
    | `BACK_LEFT` | Continue reversing, this time turning left to center the car. |
    | `FRONT_RIGHT` | Move forward slightly to straighten the car’s position. |
    | `STOP` | Vehicle is fully parked. |

    Additional states handle the case where there is no car behind or in front, allowing the algorithm to continue driving or adjust when there are no obstacles.

5. **Control Logic**:
    The control system sets two main velocities:

    - v: linear speed (forward or backward)

    - w: angular velocity (left or right rotation)

    Each maneuver adjusts these two values according to sensor feedback.
    For example:

    - In BACK_RIGHT, the vehicle moves backward (v = -2.5) while turning right (w = 4).

    - In BACK_LEFT, it continues reversing but turns left to complete the entry.

    - Finally, small forward adjustments ensure the car ends properly centered.



## Difficulties

1.**Balancing Angular Control**

Tuning the proportional constants for angular velocity (Kp_angle) and distance (Kp_dist) was challenging.
Too high values made the robot oscillate between cars, while too low values delayed the response.

2. **Detecting Empty Spots Robustly**

Small laser gaps between vehicles were sometimes mistaken for free spaces.
To solve this, I implemented the SEEN_TICKS filter and set stricter distance thresholds.

3. **Parking Path Fine-Tuning**

Defining appropriate linear and angular velocities for each maneuver required several trials to ensure the robot did not collide or over-rotate.

## Results

The final implementation successfully:

- Detects an empty space on the right side.

- Follows the line of cars maintaining a stable distance.

- Executes a complete parking maneuver without collisions.

- Adjusts its position with forward and backward corrections until fully parked.




## Parking with both cars

[Ver el video en YouTube](https://youtu.be/ZK3-ediL9ns)

## Parking with no front car

[Ver el video en YouTube](https://youtu.be/FOpQqKtzcGc)

## Parking with no back car

[Ver el video en YouTube](https://youtu.be/0xPfni9Odmg)

