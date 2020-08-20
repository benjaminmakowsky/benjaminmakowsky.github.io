---
layout: post
title:  "Getting Rvr to Change Colors on Boot"
summary: Modifying the .bashrc file to change the colors of the lights at log-in
author: Benjamin Makowsky
date: '2020-08-19 15:49:23 +0530'
category: SPARKI RVR Sphero Linux Scripting Python RaspberryPi
thumbnail: /assets/img/posts/code.jpg
keywords: introduction RVR Sphero Linux Scripting Python
permalink: /blog/Changing_rvr_color_at_login
---
In this post we are going to explore the Raspberry Pi, Linux, log-in settings, and an introduction to Python and the Rvr SDK. This is a great place to start because it allows you to have some fun with programming by creating a project you can actually see and make your own. Not to mention, once to get the basics here you can expand it to make Rvr your very own. 

# Topics:
- Raspberry Pi
- Linux
- Log-in Settings and .profile
- Python

## Raspberry Pi
The Raspberry Pi is a small compact little computer that is meant to be light weight and allows you to expand many small projects into something more complex with more capabilities. Using the Raspberry Pi, the Rvr can be taught to drive itself, use computer-vision, develop more complex AI capabilities, allow for a local web-server to run attached to the Rvr, and so much more. Part of what makes this possible is that the Operating System is a special flavor of linux called Raspian.

## Linux
Unlike MacOS and Windows which are proprietary software, Linux is mostly open-source and therefore is supported by a large community of devlopers that allow it to keep growing to suit their needs. Not only that, because it is open source it allows for a much wider possibility of user configuration and manipulation. An example of this is how easy it is to write a script or small program and have it run everytime you log in to the system. These log in settings are found in the home folder of your linux system in a file called .profile.

## .profile
This file is responsible for any set up that happens when logging in to the system. Unlike .bashrc which is ran everytime you run an interactive shell or log in, .profile is only run one and so it wont continuously try and change the color of the Rvr when connecting over ssh.

## Python
Python is a mulitpurpose programming language that has gained popularity recently due to its ease of use. We will be using python for much of the programming done because it is the language chosen by Sphero to easily connect to the Rvr. 

# Before We Start:
Explanations for connecting to the Rvr and inital set up can be found on the [Sphero Website][connecting] and in order to prevent repeating what is already out there I will not be covering things like connect with ssh to the Raspberry Pi as they have already done a great job. 

# Let's Begin

## Creating a Repository to Store Work
If you haven't already, create a repository on Github called whatever you want so that you can store all your work. 

## Connecting that Repository to Your Pi/RVR
Now, following the same instructions on the [Sphero Website][connecting] connect to your pi over ssh. If you followed the instructions on the website you should have a directory called: _sphero-sdk-raspberrypi-python_ on your pi, most likely in your home folder where ssh takes you when you first log in. Using the terminal type:
```
cd sphero-sdk-raspberrypi-python
```
Once you are inside the directory you can download or _clone_ the repository you just created in Github so that everything you make can be saved and stored online. Cloning a repository creates a copy of it on your computer where it will track any changes that you make within that folder. To do so type the following into your terminal replacing _repoName_ with the name of the repo you created on Github.
```
git clone repoName
``` 
# Commands Used:
- cd stands for Change Directory and is the command to move around your system from the command line / terminal

[connecting]: https://sdk.sphero.com/docs/getting_started/raspberry_pi/raspberry_pi_setup/#ssh-and-serial-port