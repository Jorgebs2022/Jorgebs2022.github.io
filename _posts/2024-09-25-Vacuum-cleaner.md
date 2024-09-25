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