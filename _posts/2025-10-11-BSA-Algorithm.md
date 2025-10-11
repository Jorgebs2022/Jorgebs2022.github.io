---
layout: post
title: "BSA Algorithm"
date: 2025-10-11 14:50:00 +0200
categories: robotica-movil
---

## Introduction

In this practice, the goal is to have the robot trace a movement plan throughout the house and clean as much as possible. To do this, it’s essential to generate a plan, extract the critical points, and the return points the robot must use to resume the plan.

We start from a PNG map of the house, which we need to convert into a grid map because the robot must move cell by cell.

## Plan Algorithm Overview

The navigation and planning system relied on several key components:

1. **transformation matrix**:
    - The map works in pixels (2D image), but the robot moves in world coordinates (meters). To convert between both systems, we need a “bridge.” We build that bridge from calibration point pairs (points known both in world coordinates and in the image).

    - With 3 non-collinear points we could fit an exact affine transform, but using more points lets us do a least-squares fit that averages out error. That’s why I collected 10 world↔pixel correspondences to calibrate the relationship between the map plane (pixels) and the world plane (meters).

    - The transform is a 2D affine mapping. It models translation, rotation, scale, and shear between world and pixels:
            u​=ax+by+tx
            v​=cx+dy+ty​

    where (x, y) are in world (m) and (u, v) in pixels. The 6 parameters (a, b, c, d, tx, ty) are fitted using the calibration points.

    - For each point I add two equations (one for u and one for v). With N points I have 2N equations and 6 unknowns. I solve by least squares (lstsq), which finds the values that minimize the global error between predicted and observed u, v.

    - This matrix is crucial to implement world_to_pixel(x, y, T) and pixel_to_world(u, v, T).

2. **Obstacle inflation**:
    - To reduce collision risk during navigation, obstacles are inflated so the planned path keeps a safe distance from walls and obstacles.

3. **Creating the Grid Map**:
    - The grid map is one of the most important elements. I create a NumPy matrix that stores only 0 (black) or 127 (white) per cell. Cells are 28×28 pixels. Initially I counted white and black pixels inside each cell: after scanning the cell, if there were more black than white pixels, the cell became black, and vice versa. Finally, to increase safety and avoid collisions, I changed the rule to: if there is at least one black pixel in the cell, the entire cell is black.

4. **Movement Priority**:
   - For planning, the robot follows a movement priority to be as efficient as possible. I chose the order: right, up, left, down.

5. **Critical Point and Return Point**:
   - While tracing the plan, there are times when the robot cannot move in any direction because it is either surrounded by walls or by already visited cells (the plan cannot repeat cells, so we mark visited ones). When we reach this critical point, the robot runs out of moves.

    - To solve it, we use a return point. In my case: each time the plan advances one cell, I push its neighbors onto a stack. At a critical point, I scan that stack (LIFO) to find the most recently saved neighbor that lies on an unvisited cell; at that moment I launch a breadth-first search (BFS) to compute the shortest path between the critical point and the return point. This is the only exception that allows the plan to traverse already visited cells; once it reaches the return point, the plan continues normally with the movement priority until it reaches another critical point.



6. **End Planning**:
   - Besides the grid map, I keep a boolean NumPy matrix indicating whether each cell has been visited. Initially, the whole matrix is False, and as the plan visits cells, the corresponding positions are set to True. When the entire matrix is True, the planning phase ends.

7. **Robot movement**
    - For the robot’s motion, we first obtain the bearing from the robot to the next waypoint and compute the angle error wrapped to the range [−π,π].
    The controller is simple: advance and turn. When the angular error exceeds a threshold, the robot stops linear motion and rotates until the error decreases; then it proceeds to move forward toward the target. The planner returns a list of grid coordinates which becomes the route the robot follows to cover the whole house.



## Difficulties

Several challenges arose during the development and implementation of this practice:

### Creating the transformation matrix
Due to the theoretical concepts required—both mathematical and NumPy-related—it was difficult to correctly derive and implement the transformation matrix.
### Reaching the return point
Getting the plan back to the selected return point was tricky. Initially, after reaching the critical point, the plan “teleported” to the return point and then continued. That was inefficient, so I implemented BFS to trace a shortest path from the critical point back to the return point.

### Robot mocement algorithm
At first I intended to move the robot with a VFF (Vector Field Force) approach. After realizing there were no dynamic obstacles in the practice, the most optimal solution was simply to drive the robot directly to each waypoint, assuming the grid map and obstacle inflation were correct—thus the robot would follow its route without collisions.

## Robot limitations
One major limitation is speed. If it is increased too much, it negatively affects the robot’s motion behavior.
## Conclusion

This practice provided valuable experience in implementing a planning-based navigation system. Understanding BFS, grid construction, obstacle inflation, planning, managing coordinate systems, and balancing navigation performance were key lessons. While there is room for improvement, the robot successfully navigates to its target, cleaning almost the entire house.
## Full video

[Ver el video en YouTube](https://youtu.be/ndgWXGq5848)

## photo of the map

![Texto alternativo](/assets/images/ BSA_alg.png)
