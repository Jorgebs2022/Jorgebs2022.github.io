---
layout: post
title: "Monte Carlo"
date: 2024-12-15 10:25:43 +0200
categories: robotica-movil
---

## Introduction:

In this practice, the objective was to implement a localization system based on the Monte Carlo Localization (MCL) algorithm. This method uses particles to represent the robot's position and orientation on a map and allows updating these estimates iteratively based on real sensor measurements and the robot's movement.

The main approach was to initialize the particles in a controlled manner, calculate probabilities based on theoretical and real laser data, propagate the particles with noise in each iteration, and resample them using the Monte Carlo roulette method.

## Algorithm Description:

### Particles Inicialization:

    -   The particles are generated around the central point of the map, with controlled noise in the initial position and orientation.
    -   Particles are only generated in free spaces on the map (cells not occupied by obstacles). This ensures that the particles are valid and represent possible locations for the robot.


### Laser data:

    -   Laser distances are calculated in two ways:
        -   Real laser: Data obtained directly from the robot's sensors.
        -   heoretical laser: Distances simulated using a ray tracing algorithm from each particle.

    -   The similarities between these two datasets form the basis for calculating the probability of each particle.

### Particles Propagation:

    -   In each iteration, the particles move simulating the robot's displacement. This movement includes:
        -   Noise in x,yx,y, and orientation to model the uncertainties of the movement.

    -   In this case, I implemented the robot's real odometry to improve the accuracy of the propagation.



### Weight Calculation:

    The readings from the real and theoretical lasers are compared for each particle. Particles with measurements more similar to the real laser are assigned higher weights. The weights are normalized so that their sum equals 1, creating a probability distribution.

### Resampling Particles:

    The Monte Carlo roulette method is used to select particles based on their weights. This means particles with higher weights are more likely to be selected, while those with lower weights are gradually eliminated.

### Loop Iteration:

    These steps are repeated continuously to refine the estimation of the robot's position and orientation on the map.

## Difficulties

During the implementation, several challenges arose:

### Initial Particles Validation

The main challenge when initializing particles was ensuring they all fell in free cells on the map. This required converting global to local coordinates and vice versa, and validating each particle to avoid placing them on obstacles.

### Laser Simulation

Implementing the theoretical laser using ray tracing was complex due to my initial lack of understanding. Fortunately, as I progressed through the practice, I began to grasp the true functionality and utility of the theoretical laser.

### Propagation and Noise:

Modeling the robot's motion noise to make it realistic but not excessive was critical to prevent particles from spreading too far from the actual robot.

### Roulet resampling:

Implementing the Monte Carlo roulette resampling with normalized weights presented initial difficulties, as minor errors in normalization caused imbalances in particle selection.

## Final Version:

In the final version, the system worked correctly thanks to the following improvements:

### Inicialization:

Particles are generated around the center of the map with limited noise, ensuring an accurate initial representation.

### Precise Laser Simulation:

The comparison between the real and theoretical lasers was optimized using absolute error calculations and Gaussian probabilistic functions.

### Adjusted Noise:

Particle propagation includes limited Gaussian noise in the x,yx,y positions and orientation to simulate the robot's movement.

### Eficience Resampling:

The Monte Carlo roulette adjusts the particle distribution in each iteration, favoring particles with higher probabilities and discarding less relevant ones.

## Robot Limitations:

### Computational cost:

Simulating the virtual laser for each particle is computationally expensive. Reducing the number of beams (parameter LASER_NUM_BEAMS) improves performance but affects accuracy.

### Excesive noise:

In environments with many obstacles or sensor noise, the estimation can become unstable if the particles do not converge quickly.

## Conclusion:

This practice allowed for the implementation and understanding of the Monte Carlo Localization algorithm for robots. Through iterative steps of propagation, weight calculation, and resampling, the system can accurately estimate the robot's position on a known map.

The most notable aspects of the practice include:

    -   The controlled initialization of particles in free spaces.
    -   The integration of real and theoretical laser data.
    -   The efficient use of the Monte Carlo roulette for particle adjustment.

Despite its limitations, the system is robust and functional, providing a solid foundation for future improvements.

## Full video 1st try

[Ver el video en YouTube](https://youtu.be/4VLI0E0hw-s?feature=shared)

## Full video 2nd try with less propagation noise

[Ver el video en YouTube](https://youtu.be/dbXMmLaQVIo?feature=shared)



