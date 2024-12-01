---
layout: post
title: "Obstacle Avoidance"
date: 2024-10-26 11:36:09 +0200
categories: robotica-movil
---

## Introduction

In this practice, the goal is for a racing vehicle to complete a lap on a track as quickly as possible while avoiding obstacles using only laser detection. The challenge lies in employing attractive and repulsive vectors (VFF) to guide the vehicle efficiently. The attractive vectors are based on the vehicle's coordinates relative to a target, which we convert from global coordinates to vehicle-relative coordinates. Each time the vehicle reaches a target, a new one is generated. The repulsive vectors are created using the values obtained from the laser.

The key to success in this practice is finding a balance between the attractive and repulsive forces to guide the vehicle through the track smoothly, avoiding collisions while maintaining speed.

## Difficulties

One of the major challenges encountered during the practice was managing the equilibrium between the attractive and repulsive vectors. Initially, the vehicle either collided with obstacles due to overly strong attractive forces or slowed down excessively as it over-prioritized the repulsive forces from the laser readings. The solution came from limiting the influence of the attractive vector, allowing the repulsive vector to take precedence in the VFF, ensuring smoother navigation while keeping up a good speed.

## Versions

### Version 1:

In the first version, I implemented the VFF logic where the attractive vector had significant influence over the vehicle’s direction. This approach resulted in a fast vehicle but led to frequent collisions, as the laser detection and corresponding repulsive vectors were not strong enough to prevent crashes. The vehicle was overly drawn to the target, often ignoring obstacles along the way.

### Version 2:

The second version aimed to better balance the attractive and repulsive forces. I adjusted the weight of the repulsive vectors based on the proximity of obstacles detected by the laser. However, the vehicle's performance was still suboptimal, especially around tight corners where the repulsive vectors caused the vehicle to take unnecessarily long detours, sacrificing speed.

### Final version:

In the final version, I achieved an effective balance by significantly reducing the influence of the attractive vector when the was too far away from the vehicle. This adjustment allowed the repulsive forces to dominate in critical situations, enabling the vehicle to navigate around obstacles more efficiently while still maintaining focus on reaching the target. By fine-tuning the weighting between the two vectors, the vehicle could follow a smoother and faster path.

## Robot limitations

Although the vehicle successfully completes the lap and avoids obstacles, there are limitations. The vehicle sometimes react very slow to the obstacol so the vehicle evoids it in the last momen, in the uture I would like to improve that in order to make the caar more smooth.

## Conclusion

This practice provided valuable insights into the implementation of VFF for autonomous navigation. The challenge was not only in creating the VFF itself but also in fine-tuning the balance between the attractive and repulsive forces. I learned that while attractive forces guide the vehicle toward the target, giving more control to repulsive forces in obstacle-heavy areas leads to more effective navigation.

## Full video

[Ver el video en YouTube](https://youtu.be/Cj8m2f0LExA?si=TpSygMjXOdOjhkyV)
