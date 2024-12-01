---
layout: post
title: "Follow Line"
date: 2024-10-13 11:36:09 +0200
categories: robotica-movil
---


## Introduction

The following practice consists of a series of circuits that must be completed in the shortest possible time with a racing vehicle. The objective of the practice is for the car to complete a lap in the least amount of time by following a red line on the circuit's ground. This is achieved by implementing an algorithm along with a PID controller to manage the vehicle and its oscillations, as the vehicle must follow the line as smoothly as possible. This means that with the PID, we should minimize the vehicle's oscillations to the bare minimum.

## Difficulties

The greatest difficulty I encountered throughout the practice was controlling the PID, as it operates with very small values and needs to be adjusted incrementally. Another issue was correctly applying the appropriate logic to the practice and defining the error. These reasons caused me to modify my practice from the beginning since the vehicle's performance was not optimal.

## Versions

### Version 1

In my first version, my practice consisted of a red filter that only filtered the line. My error was based on calculating the number of red pixels in the right half of the screen and the left side. The error was the difference between the pixels on the right and the left sides. This logic gave me many problems and took approximately 210 seconds to complete a lap, making the vehicle go too slowly. Additionally, the logic of my code had many issues in the curves because the red line changed on both the right and left sides, and my PID was not able to control these errors.

### Version 2

In this version, I slightly modified the logic so that instead of comparing the red pixels on the right and left sides, I take a reference image at the beginning and count the total pixels in that image. Then, I refresh the image within the loop and compare the pixels from the initial image with the pixels in the updated image. Once again, I started encountering problems when controlling the curves, and over time, I concluded that the pixel-based error calculation logic had no future. Therefore, I dedicated myself to thinking of entirely different ways to solve the problem.

### Version 3

This was my final version; I had to completely change the logic and it consisted of the following: I take a central point of the image (slightly offset because the camera is displaced) and create a vertical vector. Then, I take the highest point of the previously filtered line pixels and create a vector from the central point to the obtained point. The error is the angle formed by the two vectors within a range of -180º to 180º. With this logic, adjusting the PID was relatively easier, and I managed to achieve the expected results.

## Robot limitations

Despite its great speed and rapid error adjustment to center itself on the line, in sharp curves, I am unable to reduce its oscillation to zero without sacrificing a considerably high amount of speed. It is also unable to navigate the roads of the Ackermann tracks easily, as the PID implementation needs to be different.

## Conclusion

The conclusion I have reached after completing this practice is that often the problem is not thinking about the logic of your code to implement it in a PID, but rather that the main issue is controlling the PID adequately. Nevertheless, this practice has helped me to better understand the use of controllers and how we can apply them in simulations or in the real world.

## Picture of the time it takes to complete the race

![Texto alternativo](/assets/images/2024-10-11 12-39-33.png)

## Full video

[Ver el video en YouTube](https://youtu.be/3PXb2IvVoEk?si=-jHxxMAQegNu2f2a)
