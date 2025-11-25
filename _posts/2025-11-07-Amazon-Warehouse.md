---
layout: post
title: "Shelf Handling in an Amazon Warehouse with OMPL"
date: 2025-11-25 10:00:00 +0200
categories: robotica-movil
---

## Introduction

In this practice, the goal was to develop a **planning and control system** for two different robots operating in an Amazon-like warehouse:

- A **holonomic robot** (can move freely in the plane).
- An **Ackermann robot** (car-like, with turning constraints).

Both robots must:

1. Get the transformation matrix
2. Plan a collision-free path to a **shelf**.
3. Lift the shelf using `HAL.lift()`.
4. Plan a new path **while carrying the shelf**, now with a larger footprint.
5. Move the shelf to a **drop zone** and leave it with `HAL.putdown()`.
6. Repeat the process for the next shelf.

The core of the practice is the integration of:

- **OMPL** (Open Motion Planning Library) for global path planning.
- A **dynamic geometry model** that changes depending on whether the robot is carrying a shelf.
- A **state machine** that coordinates the sequence: go → lift → go back → place → next shelf.

The entire navigation happens inside a map generated from an image of the warehouse.

---

## Scenario and World Representation

### Map Loading and Binarization

The environment is defined by an image:

- rgb_to_bw_threshold converts the RGB image into a binary occupancy map:

- Free cells → value 127

- Obstacles (walls, shelves, etc.) → value 0

- inflated stores the map used for collision checking. The function inflate_obstacles_cv is prepared to inflate obstacles around occupied pixels, allowing to model safety margins around the robot (even though the inflation radius is 0 in this setup because at the end we quit the inflation in order to let ompl manage all the planning with geometry).

## Geometry and Collision Checking

A key aspect is that the robot footprint changes when carrying a shelf.

The procedure is:

1. Interpret the robot as a rectangle of size ROBOT_L × ROBOT_W centered at (x, y) and oriented with yaw.

2. Sample points inside this rectangle with a small step (e.g. 0.02 m).

3. Transform each point to pixel coordinates.

4. Reject the state if the point lies outside the map or the corresponding pixel in inflated is not free (!= 127).

5. When carrying_shelf = True, the rectangle grows to reflect the robot + shelf footprint, so OMPL plans paths that respect the new, larger geometry.

## Motion Planning with OMPL

### Holonomic Robot – SE2StateSpace

For the holonomic robot, I use ob.SE2StateSpace()

The planning pipeline is:

1. Bounds: inferred from dimensions = [xmin, ymin, xmax, ymax].

2. State validity: handled by isStateValid.

3. Problem definition:

- Start: current HAL.getPose3d()

- Goal: shelf or drop coordinates, with a given yaw (init_pose.yaw)

4. Planner: og.InformedRRTstar

5. Optimization: path length (PathLengthOptimizationObjective).

6. Post-processing: path interpolation to a dense set of waypoints.

This gives smooth paths in SE(2) without kinematic constraints, suitable for a holonomic platform.

### Ackermann Robot – ReedsSheppStateSpace

The Ackermann robot uses ob.ReedsSheppStateSpace(min_turning_radius) with a min_turning_radius equal to 0.2.

This space models a car-like robot:

- Limited turning radius.

- Possibility of going forward and backward.

- More realistic trajectories for a vehicle with Ackermann steering.

## Executing the Path: Local Navigation

Once a path is obtained, I convert it to a list of states

The function robot_nav(states) is responsible for following the waypoints:

1. Compute the distance and bearing to the current waypoint.

2. Calculate the angular error

3. Apply a simple control law

4. When the robot is close enough to the waypoint (dist < 0.15), it advances to the next one.

5. If it was the last waypoint:

    - Resets current_wp.

    - Advances the global robot_state.

    - If it was returning with a shelf, calls HAL.putdown() and updates the map to draw the shelf in its new location.

For the Ackermann robot, the navigation logic slightly changes when carrying a shelf, reducing V_MAX and adjusting gains to make the motion slower and safer with the increased footprint.

## Dynamic Map Updates: Removing and Placing Shelves

To keep planning realistic, the map is modified on the fly:

1. When a shelf is taken:

    - clear_shelf_in_map(world_x, world_y) marks the region previously occupied by the shelf as free (127) in inflated.

2. When a shelf is placed in the drop area:

    - show_shelf(world_x, world_y) draws the four legs of the shelf inflated by setting those pixels to occupied (0).

This ensures that:

- Future plans don’t collide with shelves that are no longer there.

- The shelves left in new positions become new obstacles for subsequent planning.

## Difficulties

### Balancing Planning Time and Path Quality

Using InformedRRTstar for every movement can be expensive.

I had to tune:

- Maximum solving time (solve(10.0) / solve(15.0)).

- Path interpolation density.

- Smoothing steps in the Ackermann robot.

The goal was to obtain smooth, feasible paths without excessive computation time.

It was also difficult to find the best planning which fitted the most to our robot at the warehouse

### State Machine Synchronization

Ensuring that the transitions between states (SHELVE, LIFT, BACK, PLACE) happened at the right moment required careful control of:

- Distance thresholds for waypoint completion.

- Timing in the back_in_shelf() maneuver.

- Correct use of flags like make_plan_one_time and carrying_shelf.

### World–Pixel Consistency

Small errors in the calibration between world and pixel coordinates (coord_pts vs pixel_pts) can cause collisions that don’t seem to exist visually.
Getting a good fit for the affine transformation T and validating it on several points was essential.

## Conclusion

Both the holonomic and the Ackermann robots share the same global idea, but solve it with different kinematic models and slightly different maneuvering strategies, demonstrating how OMPL and geometric reasoning can be adapted to different types of robots in a realistic warehouse scenario.

## Full videos

### Ackermann
[Ver el video en YouTube](https://youtu.be/eRG7bSb0HqQ)

### Holonomic
[Ver el video en YouTube](https://youtu.be/ldGkpcUt97k)
