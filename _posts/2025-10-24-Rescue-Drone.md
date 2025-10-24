---
layout: post
title: "Rescue Drone"
date: 2025-10-24 14:50:00 +0200
categories: robotica-movil
---

## Introduction

This project consists of a drone that must fly over a water area where several civilians are located. The drone must use face detection to identify their positions and keep count of the civilians found.


## Plan Algorithm Overview

The navigation and planning system relied on several key components:
1. **Locate drone and survivors**
    - To locate both the drone (the boat’s position) and the survivor area, we are given the GPS coordinates of both locations (40º16’48.2” N, 3º49’03.5” W for the boat, and 40º16’47.23” N, 3º49’01.78” W for the survivors).
    - Using the UTM library, and based on latitude and longitude, we can obtain the distances in the East and North directions. Through a series of calculations, we then convert these GPS coordinates into metric coordinates for the robot.

2. **Takeoff**:
    - A simple action where the drone takes off by calling the function HAL.takeoff().

3. **Reach survivors**:
    - Once airborne, the drone reaches the survivor area by moving toward the target position using the function HAL.set_cmd_pos(x, y, z, az).
    - To ensure that the drone always faces its target, I implemented a function that calculates the necessary yaw angle to keep it correctly oriented during flight.

4. **Drone Movement**:
    - For the drone’s search pattern, I chose a spiral movement from the inside out, allowing it to cover the largest possible area and increase the chances of detecting survivors.

5. **Face Detection**
    - For face detection, I used OpenCV’s Haar Cascades. To improve detection performance, I created a color mask that replaces the sea (blue areas) with black.
    - In the video shown at the end of this post, this masking process can be seen more clearly.

6. **Battery**:
   - The drone is equipped with a battery that gradually discharges over time. When the battery level drops below 40%, the drone automatically returns to base, lands, recharges, and resumes the search.

## Difficulties
- The main challenge I faced in this project was fine-tuning the face detection parameters, as they had to be adjusted very precisely to ensure accurate detection.

- I also found it somewhat difficult to understand the conversion from GPS coordinates to metric coordinates, as it was the first time I had encountered this process.

## Full video

[Ver el video en YouTube](https://youtu.be/WRnUdnYlmBA)

