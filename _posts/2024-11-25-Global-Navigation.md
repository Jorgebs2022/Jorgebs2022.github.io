---
layout: post
title: "Global Navigation"
date: 2024-11-25 10:25:43 +0200
categories: jekyll update
---

## Introduction

In this practice, the objective was to navigate a vehicle to a given global coordinate. The navigation was performed using a cost grid, where the map expands using a breadth-first search (BFS) algorithm to calculate the path costs from the target. This approach allowed the vehicle to efficiently determine the optimal path while avoiding obstacles.

The practice was centered around processing and integrating the map data, cost calculations, and navigation logic into a unified system. Once a global coordinate target was provided, the vehicle autonomously planned and executed its path.

## Algorithm Overview

The navigation system relied on several key components:
1. **Cost Grid Expansion**:
   - The map was expanded using BFS starting from the target position. Each cell in the grid was assigned a cost based on the distance to the target and the presence of obstacles.

2. **Obstacle Inflation**:
   - To ensure safe navigation, the algorithm inflated obstacles by increasing the cost of cells around them. This prevented the vehicle from navigating too close to obstacles, providing a safety buffer.

3. **Global to Local Coordinate Conversion**:
   - The practice used global (world) coordinates for the target and local (relative) coordinates for the vehicle's movement. A key aspect was converting these coordinates correctly for seamless navigation.

4. **Path Selection**:
   - The vehicle evaluated neighboring cells to find the one with the lowest cost and moved towards it iteratively until it reached the target.


## Difficulties

Several challenges arose during the development and implementation of this practice:

### Understanding the Cost Grid
The first major challenge was grasping how the cost grid functioned and how the BFS expansion calculated the costs. The key was understanding that lower costs indicated paths closer to the target, while higher costs represented areas to avoid due to obstacles or long distances.

### Coordinate Systems
Another significant hurdle was managing the coordinate systems. The practice operated in terms of **rows and columns** (\(y, x\)) rather than the typical Cartesian coordinates (\(x, y\)). This discrepancy often led to bugs when interpreting the vehicle's position on the map. Ensuring consistency across all coordinate conversions was crucial for accurate navigation.

### Navigation Challenges
The navigation itself was a tedious process. Fine-tuning the inflation of obstacles and balancing the vehicle's path cost evaluations took considerable effort to achieve smooth, collision-free motion. Despite this, some edge cases with closely spaced obstacles remained challenging.


## Versions

### Version 1:
Initially, the BFS expansion and cost grid inflation were implemented. While the vehicle successfully followed the path, it frequently collided with obstacles due to insufficient obstacle inflation and inaccuracies in coordinate handling.

### Version 2:
The next iteration addressed the obstacle inflation. Costs around obstacles were increased significantly, creating a buffer. Additionally, the conversion between global and map coordinates was carefully debugged. This improved the vehicle's performance but revealed inefficiencies in pathfinding.

### Final Version:
In the final version, all components were fine-tuned:
1. The BFS expansion was optimized for better performance.
2. The vehicle's navigation logic was streamlined, ensuring smoother motion and more precise turns.
3. Conversion between global and map coordinates was made robust, avoiding errors caused by the \(x, y\) and \(y, x\) mismatch.

## Robot limitations

Despite the improvements, the vehicle occasionally reacts too late to obstacles, leading to last-moment adjustments. While the navigation is functional, future iterations could focus on smoother obstacle avoidance and faster computation of the cost grid.


## Conclusion

This practice provided valuable experience in implementing a navigation system based on a cost grid. Understanding BFS for grid expansion, managing coordinate systems, and balancing navigation performance were key lessons learned. While there are areas for improvement, the vehicle successfully navigates to its target while avoiding obstacles.

## Full video one trip

[Ver el video en YouTube](https://youtu.be/ZvjFWqf-dd8)

## Full video two trips

[Ver el video en YouTube](https://youtu.be/APqx-dhWYkQ)
