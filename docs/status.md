---
layout: default
title: Status
---

## Summary of the Project

Duckietown is an interactive platform for developing and evaluating algorithms for autonomous driving tasks like Lane Following alongside other vehicles and/or pedestrians. The goal of our project was to train an agent (a duckie bot) to drive on the right side of the street while avoiding pedestrians. Because of time constraints and challenges that arose, the updated goal is to train an agent to drive on a street following one lane (basic lane following). The input from the Duckietown simulator is an image in the form of a NumPy array for each timestep. The output from our agent is a pair of motor commands specifying the velocity and direction that our duckie bot should follow for each timestep. Currently with the DDPG algorithm, the agent does not train well and we are planning on tuning hyperparameters and the reward function to improve upon the algorithm to reach our goal.

## Approach

One of our first approaches was through a Linux virtual machine. Not enough memory could be allocated since the requirement for reinforcement learning is 8 GB of RAM but we could only allocate 4 GB due to the limited resource on our laptops. Training would halt and the simulator was not running properly. Another approach was through the UCI JupyterHub. Originally, we ran into issues trying to work with Nvidia Docker as it required sudo commands. A few days later, JupyterHub had been updated to allow sudo commands but we continued to have issues with running the sudo commands needed to download the Nvidia docker and other dependent packages. However, we were able to download the pre-built Docker image which allowed us to run a manual version of the duckietown simulator, but this setup was extremely laggy and we decided it wasn’t a viable solution. Our final approach was to run our training algorithm on GPU-accelerated AWS servers. This took us a few days to set up since it required increasing vCPU limits via AWS Service Center. The main limitation of this approach is that the Duckietown simulator cannot run in this environment - it requires a local Ubuntu machine which will need to be set up through dual booting. However, we have decided to use the episodic reward returns as the evaluation technique instead of the simulator.

In order to train our agent to learn to drive on the right side of the road, we decided to use Deep Deterministic Policy Gradient (DDPG) as the base algorithm. DDPG is similar to the Q-learning algorithm as it tries to learn a Q-function and find a policy that can maximize total episode rewards over many time steps. However, DDPG specializes in finding an optimal policy for environments like Duckietown where there are an infinite number of actions the agent can take, i.e. environments with continuous action spaces. The DDPG algorithm makes use of the actor-critic model to work well with continuous action spaces where the actor (or the policy) is used to select the next action to be taken and the critic (or the Q function) is used to evaluate how good the action taken really was. Below are representations of the Duckietown simulator and what the agent sees while deciding on the next action.

![data-from-img-finalmain-ed9549e9](https://user-images.githubusercontent.com/35225535/142749079-7d064222-8a4f-4919-8873-d8a374544f25.gif)


The reward function is based on the agent driving along the right side of the road without going off the road or leaving its lane. The agent is penalized for swaying while moving forward or straying away from the center of the right lane. The agent receives a reward of -1000 for going completely off the road. The DDPG algorithm is a great choice for this problem because it requires very little computation to select the next action. More specifically, the expected reward after taking an action at in state st and following policy π is:

![Capture](https://user-images.githubusercontent.com/35225535/142749082-991b7e52-7696-4c18-a458-802f2798d220.PNG)


## Evaluation

### Quantitative Evaluation
Our goal is to have our agent learn to stay in the lane, and how to take turns. The duckie bot must finish the course in a reasonable time as well. Rewards will be given according to the action that the duckie bot takes in a range of  [-100,100]. The value of the reward is based on how well the duckie bot stays in the lane, and how well it takes turns. Actions like going completely off the road will result in a negative penalty and the duckie bot will be reset to the start of the course. In conclusion, the metrics for evaluation depend on the distance traveled, survival time, etc.

In order to have a quantitative analysis of our bot’s driving abilities we will be looking at the rewards received. We will be fine tuning the hyperparameters and training in order to gather the rewards data. We will plot the results from the training to understand how adjusting various hyperparameters affects our bot’s results. By plotting the results, and comparing results from different fine-tunings we will be able to identify how beneficial each adjustment to the hyperparameters ends up being. 

### Qualitative Evaluation
In order to have a qualitative analysis of our project, we wanted to get the Duckietown simulator running to see how our bot was navigating. However, since we ultimately decided to train our bot on the AWS servers, we learned that the only way to get the simulator to work would be through setting up dual booting on our local machine. Due to the time limitations, the advice we’ve gotten, and the issues we’ve encountered throughout the set up process, we have decided to not set up the simulator and rely on other factors for qualitative analysis.

Another part of our qualitative analysis comes from the training results. As our agent trains, we get detailed feedback on the bot’s performance. Some of the information we receive tells us whether the bot was on drivable terrain, the coordinates of the bot respective to the map and whether it got into any collisions. These details give us an understanding of the quality of our bot’s performance as it trains.


## Remaining Goals & Challenges
Currently the prototype is not achieving the main goal that we had set due to many limitations and challenges that we had and currently are facing. A lot of our time was focused on setting up the environment for this project and understanding how the reinforcement learning template for duckietown works. We did not have the chance of running the training due to AWS approval issues. Our next goal is to start training the agent and test which set of hyperparameters will achieve sufficient lane following. We may also attempt the suggestions given by the official duckietown documents such as modifying the reward function or editing the image taken. 

One challenge we may face is conducting enough tests within the remaining time. To train the agent it takes an utmost of 5-6 hours and that’s without knowledge of whether the changes we added would improve lane following. The instance that we are using on AWS is also a paid service, which also makes the training time limited. The output from training is difficult to interpret as well and determining whether the duckietown is properly trained through the reward amount received may not be a suitable method. The simulator would have been best however, we were unable to run it on our local machine. If we do face issues in training, we could consider a duckietown map with a single straight lane for the agent to follow.


## Resources Used
* Helpful ML/AI libraries: Gym, Numpy, Torch
* https://arxiv.org/pdf/1509.02971.pdf
* https://spinningup.openai.com/en/latest/algorithms/ddpg.html#
* https://github.com/kaland313/Duckietown-RL
* https://docs.duckietown.org/daffy/AIDO/draft/embodied_rl.html
* https://github.com/duckietown/challenge-aido_LF-baseline-RL-sim-pytorch
