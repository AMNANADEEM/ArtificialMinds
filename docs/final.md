---
layout: default
title: Final Report
---

## Video
[Use a level two header at the very top, and embed the video of your project using the YouTube embed
video option. Specifically, go to your video on YouTube, then click on Share, then Embed, and copy-paste
the code in final.md . As in the status report, the video should contain a brief problem description (using
images, screenshots, or screen captures), an example capture of how a simple baseline performs, and an
example capture of a run that is your best. You are free to include more details, such as summary of how you
did it, some of the failure cases, but it is not needed. The video should be a maximum of three minutes
(less is fine), of reasonably high quality, i.e. a minimum resolution of 1200 ×720 (i.e. 720p), and speech, if
any, should be comprehensible.]
## Project Summary
Duckietown is an urban environment platform that allows people to train DuckieBots to drive on various roads and navigate unseen routes. This platform has been used widely for research on autonomous driving and physically embodied AI systems. Typically, DuckieTown is configured as a physical platform, with hardware being provided to build tracks and low-cost mobile robots being used as DuckieBots. For the purposes of our project, we decided to use DuckieTown’s virtual simulator to train and test our model’s (aka DuckieBot’s) performance. 

Initially, the goal of our project was to train an agent (a DuckieBot) to drive on the right side of the street while avoiding pedestrians. With this goal, we were taking on a number of big goals at the same time - teaching the agent to drive straight following its lane, while being on the right side of the lane, and identifying and stopping for pedestrians. Realizing that just one of these areas is challenging on its own, we tweaked our goals to train an agent to just drive on a street following one lane. At the beginning of the quarter, we asked ourselves why machine learning is useful for a lane following bot. Machine learning algorithms make an autonomous bot or vehicle capable of making real-time decisions based on the inputs given to it. More specifically, autonomous driving is an interesting application for reinforcement learning algorithms. Our final goal was to use reinforcement learning to teach our DuckieBot to follow the lane that it starts in, using rewards and punishments as learning metrics.

The inputs provided to our agent from the Duckietown simulator are 640 x 480 resolution images in the form of a NumPy array for each timestep. These images are produced from the 160 degrees fish-eye lens on the front of the bot at a rate of 30 frames/second. The output from our agent is a pair of motor commands specifying the velocity and direction that our DuckieBot should follow for each timestep. 

## Approaches
In the process of setting up a viable duckietown environment, our team ran into several issues early on. Since duckietown is not supported on Windows, we attempted to use a Linux virtual machine, then we tried to use UCI’s JupyterHub, and finally AWS as suggested by Dailin and the professor. AWS seemed to be the only reasonable option to run the duckietown environment. Despite being our best option, it still wasn’t quite ideal as we weren’t able to run a simulator to visualize what our duckiebot was doing throughout training.
Even with AWS we ran into a few set up issues as our initial training attempts were not able to go past 6 epochs. After reaching out to Dailin we learned that we should have used a g4 instance rather than the free t2 instances we had all started off with. In order to get access to g4 instances, we each needed to request for a vCPU limit increase from AWS. Due to increasing costs of running these more demanding instances, we reached out to Professor Fox, who provided us with a group AWS account. Since this was a new account, we had to once again request a vCPU limit increase, but were initially denied since we had not used the AWS account yet. So then we ran a t2 instance prior to re-requesting the vCPU limit increase. Once we received the vCPU limit increase, had access to the base AMI from Dailin, and were able to start training we realized that we could only run a single training at a time. Since each training we planned to run would approximately take 5-7 hours, we had to coordinate when we could run each training with the changes.   
In order to train our agent to learn to drive on the right side of the road, we decided to use Deep Deterministic Policy Gradient (DDPG) as the base algorithm. Dailin gave us access to the base AMI with the duckietown environment and a trainable DDPG baseline. DDPG is similar to the Q-learning algorithm as it tries to learn a Q-function and find a policy that can maximize total episode rewards over many time steps. However, DDPG specializes in finding an optimal policy for environments like Duckietown where there are an infinite number of actions the agent can take, i.e. environments with continuous action spaces. The DDPG algorithm makes use of the actor-critic model to work well with continuous action spaces where the actor (or the policy) is used to select the next action to be taken and the critic (or the Q function) is used to evaluate how good the action taken really was. Below are representations of the Duckietown simulator and what the agent sees while deciding on the next action.


![data-from-img-finalmain-ed9549e9](https://user-images.githubusercontent.com/35225535/142749079-7d064222-8a4f-4919-8873-d8a374544f25.gif)

The reward function is based on the agent driving along the right side of the road without going off the road or leaving its lane. The agent is penalized for swaying while moving forward or straying away from the center of the right lane. The agent receives a reward of -1000 for going completely off the road. The DDPG algorithm is a great choice for this problem because it requires very little computation to select the next action. More specifically, the expected reward after taking an action at in state st and following policy π is:


![Capture](https://user-images.githubusercontent.com/35225535/142749082-991b7e52-7696-4c18-a458-802f2798d220.PNG)

In our attempts to improve our baseline training policy we tried manipulating the rewards and tuning the hyperparameters. 

We started with modifying the reward function of the algorithm so as to exaggerate positive and negative rewards received for various actions. We gave the model a reward of +150 for following the lane at a decent speed without going off the road, a reward of +70 for driving forward even at lower speeds and a negative reward of -100 for steering off the road or hitting an obstacle. When it came to modifying the rewards, we recognized that we would be significantly altering the way our results looked. Despite the rewards presenting as positive, we had to make sure we were analyzing whether there was improvement in our duckiebot’s ability to follow the lane.

For our next two models, we attempted tuning different hyperparameters to help exploration. 

For one of those models we just increased expl_noise, which is the standard deviation for Gaussian exploration noise added to policy at training time. The default value was 0.1, which we changed to 0.3. 

For the other model we just increased start timesteps, which should have increased the number of steps where the DuckieBot was making uniform-random action selections prior to running the actual policy. The default value was 1e4, which we changed to 1.5e4.

We were hoping that from these first 3 models we would be able to determine what changes to the baseline DDPG training policy improved our Duckiebot’s driving the most. We wanted to combine the changes that gave us the most significant improvements in order to train an even better model. Unfortunately, none of the models showed clear improvements over time. Because the previous models did not show improved results individually, we thought to combine models anyway for the chance of an improved model. For the last two models, we kept the same hyperparameters that we adjusted from previous models and incorporated the modified rewards. However, these models did not show improvements either. 

Since we did not learn much from the first 5 models that we trained, we wanted to try a different approach by manipulating the observation image and converting it into a grayscale image. There were several wrappers that controlled the rewards, manipulated the image, and other tasks, so we decided to add one that converted the image. While we were able to put the image into grayscale, we were not able to successfully return the correctly sized array and were unable to actually run training with that change due to limits on time. The following is an example of our implementation to convert the image.


![graywrapper](https://user-images.githubusercontent.com/45059021/145662211-859a64f9-08ed-4c31-abfd-6600f1f5dee3.jpg)


## Evaluation
Quantitative:
Measuring the performance of our trained agent was one of the harder parts of this project since metrics for evaluating an agent’s driving and lane following abilities is not clear-cut and depends on a variety of factors like the quality of the image input. In order to have a quantitative analysis of our bot’s driving abilities, we focused on the rewards collected by our DuckieBot both during training and testing. As we fine-tuned various hyperparameters of the model, we recorded the rewards received by the agent and visualized this data in the form of linear graphs to be able to identify how beneficial each adjustment to the hyperparameters was and how to further improve the model. Below we discuss some of the results of training and testing our agent with the baseline DDPG algorithm and with some hyperparameters tuned.

With our baseline off-policy algorithm (i.e. DDPG), we were able to train our model to make decisions in continuous action spaces. The following is a visualization of the testing rewards over a small period of timesteps. 

![test-rewards-base](https://user-images.githubusercontent.com/35225535/145662315-c63c1fb7-1e44-4592-8314-1f3507729366.png)

The results of the baseline model were majorly inconclusive - we were unsure of whether the model was overfitting or the inputs needed to be improved on. We decided to tune various hyperparameters to get a better understanding. The specific ways in which we tuned hyperparameters are mentioned in the Approaches section. Below are some of the rewards plots from testing our trained models. The graphs are heavily populated since they are over many timesteps. However, it is clear to see that the changes we made to our hyperparameters did not help the agent get much better at following the right side of the street. In the first graph, where we modified the rewards function, it initially looked like a step in the right direction due to skewed positive results. However, the body of the graphs suggests that the model may still be overfitting. For this reason, in the following trainings, we decided not to exaggerate the rewards and focus on other hyperparameters (specifically the exploration noise and the start timesteps).


![test-rewards-tanya-1](https://user-images.githubusercontent.com/35225535/145662318-6c9152a8-a0b8-4ee0-a64f-0bcffb578161.png)

Testing Results (with modified rewards function)


![test-rewards-saman-2](https://user-images.githubusercontent.com/35225535/145662403-8ee8df61-031b-4691-af2f-7888cdf3aff7.png)

Testing Results (with increased start_timesteps)


![test-rewards-amna-2](https://user-images.githubusercontent.com/35225535/145662408-32f80ebf-57d6-4bcf-af82-ee2807fb5533.png)

Testing Results (with increased expl_noise)


Qualitative:
In order to have a qualitative analysis of our project, we wanted to get the Duckietown simulator running to see how our bot was navigating. However, since we ultimately decided to train our bot on the AWS servers, we learned that the only way to get the simulator to work would be through setting up dual booting on our local machine. Due to the time limitations, the advice we’ve gotten, and the issues we’ve encountered throughout the set up process, we have decided to not set up the simulator and rely on other factors for qualitative analysis.

Another part of our qualitative analysis comes from the training results. As our agent trains, we get detailed feedback on the bot’s performance. Some of the information we receive tells us whether the bot was on drivable terrain, the coordinates of the bot respective to the map and whether it got into any collisions. Below is a snippet of the training debugging output that we used internally for quantitative analysis. These details give us an understanding of the quality of our bot’s performance as it trains.

![unnamed](https://user-images.githubusercontent.com/35225535/145662381-6ca13b8d-29a0-4df0-a13f-7d949d581891.png)


## References
Research papers on DDPG, SAC and other algorithms:
DDPG implementation of rl template: https://arxiv.org/pdf/1509.02971.pdf
Past champions: https://github.com/kaland313/Duckietown-RL
Duckietown repo, documentation manual
https://docs.duckietown.org/daffy/AIDO/draft/embodied_rl.html
https://github.com/duckietown/challenge-aido_LF-baseline-RL-sim-pytorch
DDPG information: https://spinningup.openai.com/en/latest/algorithms/ddpg.html 
ML libraries: gym, numpy, torch
