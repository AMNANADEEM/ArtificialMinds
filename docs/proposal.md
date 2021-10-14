---
layout: default
title: Proposal
---

## Summary of the Project

Our goal is to make our Duckiebot drive in a lane without hitting pedestrians. The input from the simulator will be an image for each timestep in the form of a NumPy ndarray with resolution 160x120x3 in range [0,255] with RGB encoding. The output from our project will be a pair of motor commands for each timestep corresponding to the velocity and steering angle that our Duckiebot should then follow. The main real world application of our project would be in autonomous driving while avoiding obstacles. 

## AI/ML Algorithms

Deep reinforcement learning using image processing and Neural Networks

## Evaluation Plan

Quantitative evaluation
At the start, the Duckiebot will have to learn to avoid hitting pedestrians, stay in the lane, and how to take turns. Once it learns, we expect it to maintain a constant speed throughout the course, be able to slow down at turns, and stop when a pedestrian is seen. The Duckiebot must finish the course in a reasonable time as well. Rewards will be given according to the action that the Duckiebot takes in a range of  [-100,100]. The value of the reward is based on how well the Duckiebot stays in the lane, how well it avoids pedestrians, and how well it takes turns. Going completely off the road and/or hitting a pedestrian will end up with a negative reward and the Duckiebot will be reset to the start of the course. 

