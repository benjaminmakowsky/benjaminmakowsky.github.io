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
- Log-in Settings and .bashrc
- Python

## Raspberry Pi
The Raspberry Pi is a small compact little computer that is meant to be light weight and allows you to expand many small projects into something more complex with more capabilities. Using the Raspberry Pi, the Rvr can be taught to drive itself, use computer-vision, develop more complex AI capabilities, allow for a local web-server to run attached to the Rvr, and so much more. Part of what makes this possible is that the Operating System is a special flavor of linux called Raspian.

## Linux
Unlike MacOS and Windows which are proprietary software, Linux is mostly open-source and therefore is supported by a large community of devlopers that allow it to keep growing to suit their needs. Not only that, because it is open source it allows for a much wider possibility of user configuration and manipulation. An example of this is how easy it is to write a script or small program and have it run everytime you log in to the system. These log in settings are found in the home folder of your linux system in a file called .bashrc.

## .bash_profile
