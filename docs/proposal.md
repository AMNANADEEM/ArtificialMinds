---
layout: default
title: Proposal
---

## Summary of the Project

The goal of our project is to train an agent (a duckie bot) to drive on the right side of the street while avoiding pedestrians. The input from the Duckietown simulator will be an image in the form of a NumPy array for each timestep. The output from our agent will be a pair of motor commands specifying the velocity and direction that our duckie bot should follow for each timestep. The applications of this project include research into autonomous driving while pedestrians are present.

## AI/ML Algorithms

We plan on using deep reinforcement learning and neural networks alongside image processing techniques like object detection and classification to teach our agent to stay on the right side of the road and identify pedestrians in order to avoid them.


## Evaluation Plan

### Quantitative Evaluation
Our goal is to have our agent learn to avoid hitting pedestrians, stay in the lane, and how to take turns. The duckie bot must finish the course in a reasonable time as well. Rewards will be given according to the action that the duckie bot takes in a range of  [-100,100]. The value of the reward is based on how well the duckie bot stays in the lane, how well it avoids pedestrians, and how well it takes turns. Actions like going completely off the road, failing to identify or hitting a pedestrian will result in a negative penalty and the duckie bot will be reset to the start of the course. In conclusion, the metrics for evaluation depend on the distance traveled, survival time, and number of pedestrians successfully avoided by our trained agent.

### Qualitative Evaluation
At the start, we expect our agent to be going off the road often, not being able to follow the right lane and not avoiding pedestrians.  Eventually, we expect it to maintain a constant speed throughout the course, be able to slow down at turns, and stop at a responsible pace when a pedestrian is identified. The moonshot case for our project would be if our agent learnt to drive on different road maps at a relatively faster speed, making quicker decisions while identifying close to 100% of the pedestrians crossing the road and stopping well before in a safe manner.

## Meeting #1 (w/ Prof. Roy Fox)
Thursday, October 21, 2021 at 02:00pm (Pacific Time - US & Canada)

Zoom link: https://uci.zoom.us/j/8333245266?pwd=RnhFQU9qanRGZ0lCL1lYZnNPVmk
