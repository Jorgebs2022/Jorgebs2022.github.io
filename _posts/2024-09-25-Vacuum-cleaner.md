---
layout: post
title: "Vacuum cleaner"
date: 2024-09-25 19:39:09 +0200
categories: jekyll update
---

Welcome to my first Robotics Mobile Blog!

## Introduction

Mobile robotics is a fascinating and constantly evolving field that combines engineering, computer science, and artificial intelligence to develop robotic systems capable of moving and operating in real-world environments. Through the integration of sensors, actuators, and control algorithms, mobile robots can autonomously navigate, make real-time decisions, and perform specific tasks, ranging from exploring unknown terrains to delivering goods.

In this case, I am going to showcase a practice involving a vacuum cleaner without a localization or mapping system, but equipped with a bumper system and a laser. The objective of the practice is for the vacuum cleaner to cover as much space as possible within a room in a time interval set by the examiners, using an algorithm created by me, with the only assistance coming from the data obtained from the bumper and the 180° laser.

## Project Objective

My intention in doing this practice is to assess my ability to create an algorithm capable of fulfilling the requirements set by the examiners in the most efficient way with the minimum resources provided. However, even though we were only able to use the bumper and the laser, I didn't want to limit myself to a single implementation, such as only using the bumper or only using the laser. I decided to use both sensors so that I could demonstrate the knowledge I have gained about them during these years of study.

## Difficulties

One of the biggest challenges for me in this practice has been the implementation of the laser, as from the very beginning, I wasn't quite sure how I was going to implement it. Without the ability to localize the robot or having a map, I didn't feel that the laser had any useful purpose. Through coding trials, I concluded how to best implement the laser in my code. By obtaining the central value of the laser (the position 90 of the laser data array), I could allow the robot to detect what is right in front of it. Thanks to this implementation, my code was able to function in a much smoother and more efficient manner.

Another significant problem I faced was the creation of my state machine, as I wasn't initially clear about what states I would have and how the machine would react to the data obtained from the laser and the bumper. Over time, I realized that only through trial and error can you arrive at a state machine that may convince you and perform what you have in mind. It's one thing to have an idea in your head of what you want your robot to do and another to make it happen for real through code.

## System Description

My algorithm primarily focuses on covering as many rooms in the house as possible while passing through the maximum number of areas using the bumper and laser. However, it does not aim to cover every single space, as this would cause the robot to repeat movements and pass through areas it has already covered.

### Robot States

As my robot's algorithm is designed, it consists of the following behaviors:

### Spyral State:

In this state, the robot creates a spiral pattern to cover the maximum amount of space until it collides with a wall, at which point it switches to a collision state.

### Collision State:

This state keeps track of the number of times the robot collides with a wall and moves the robot backward to avoid continuous collisions. At this point, the robot can switch to two different states: it usually transitions to a right-turn state, but if the collision counter reaches a certain number, it will switch to a turn state.

### Right-Turn State:

In this state, the robot moves forward while turning to the right until it collides with a wall, at which point it returns to the previous collision state.

### Turn State:

When the collision counter reaches a certain number, this state stops the robot and gives it only angular velocity, making it turn until the central part of the laser detects enough space to move straight. In that case, it switches to a forward state.

### Forward State:

In this state, the robot moves in a straight line until the central part of the laser detects enough space to start a spiral again, at which point it switches back to the spiral state.

## Difficulties

The biggest challenge I encountered throughout this practice was the implementation of the laser. From the beginning, my intention was to implement both the bumper and the laser, but since the robot didn't have a map, I wasn't sure where to implement it. Another major issue was controlling the collisions, as initially, the counter didn't work as I expected due to the robot's iteration speed.

## Robot Limitations

Although the robot is able to pass through all the rooms in the house, it is not capable of performing a thorough sweep because I didn't want it to repeat the areas it had already covered.

## Work Done In 10 Minutes

![Texto alternativo](/assets/images/2024-09-26 10-31-56.png)
![Texto alternativo](/assets/images/2024-09-26 10-49-00.png)
