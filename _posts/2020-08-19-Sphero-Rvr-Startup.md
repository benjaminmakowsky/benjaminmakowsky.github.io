---
layout: post
title:  "Getting Rvr to Change Colors on Boot"
summary: Modifying the .bashrc file to change the colors of the lights at log-in
author: Benjamin Makowsky
date: '2020-08-19 15:49:23 +0530'
category: SPARKI 
thumbnail: /assets/img/posts/JavaScript.png
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
Once you have cloned your repo _cd_ into it so that you can begin working. Next we are going to make a directory inside your project called Python, where you can keep all your python code and scripts. To do this type the follow:
```
mkdir Python && cd Python
```
Here _mkdir_ makes a directory called mkdir, the _&&_ states you want to do another command, and then you _cd_ into the directory you just made. Once inside your new Python directory you want to create a new file using the _touch_ command as shown:
```
touch set_start_up_led_color.py
```
This will create a file called _set_start_up_led_color.py_ which we will use, as the name implies, to set the color of the lights on start-up. Copy the code either from either [here][file], or from the sections below.

## Import Statments:
These import statements allow you to use code from other modules (python files) so that you don't have to write everything yourself.
```python
import os
import sys
sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), '../../')))

import asyncio
from sphero_sdk import SpheroRvrAsync
from sphero_sdk import Colors
from sphero_sdk import RvrLedGroups
from sphero_sdk import SerialAsyncDal
```
__NOTE:__ 
```
sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), '../../'))) 
```
This line tells your program to look 2 directories up for files when importing. If your folder is not in:
__sphero-sdk-raspberrypi-python/myrepo/Python__ it won't find all the files necessary.

## Initialize Global Variables
```python
loop = asyncio.get_event_loop()
rvr = SpheroRvrAsync(dal=SerialAsyncDal(loop))
```
The first line fetches the program loop that will be used later on to repeat until the program exits. The second line creates a rvr ___OBJECT___. An object is exactly like it sounds, it is the code representation of an object that you can modify with code.

## The Main Program
```python
async def main():
    
    #Wake up the rvr
    await rvr.wake()

    # Give RVR time to wake up
    await asyncio.sleep(2)

    # Sets all the colors using RGB style
    await rvr.set_all_leds(
        led_group=RvrLedGroups.all_lights.value,
        # 255, 25, 0 Makes the color orange
        led_brightness_values=[color for x in range(10) for color in [255 , 25, 0]]
    )

    # Delay to show LEDs change
    await asyncio.sleep(1)
    await rvr.close()
```
We will discuss the terms async and await later as they require a whole post by themselves, you just need to know that they allow multiple lines of code to be executed without having to be run in order one after the other.

```python
if __name__ == '__main__':
    try:
        loop.run_until_complete(main())

    except KeyboardInterrupt:
        print('\nProgram terminated with keyboard interrupt.')

        loop.run_until_complete(rvr.close())

    finally:
        if loop.is_running():
            loop.close()
```
The above section simply states if this script is the main program loop until all the lights and change and stop if you interupt using a keyboard.

# Running the Program at Start-Up:
Running your program at start up is the easy part of this process. Because of the way Sphero works you have to load the environment just like this instructions say when setting up for the first time. What we need to do is determine where on our pi this is stored so that we can call it from wherever we want. To do this type the following in the terminal:
```bash
ls /home/pi/.local/share/virtualenvs
```
This should display some text that looks like:
```
sdk-raspberrypi-python-M5SuJw2V
```
This is the name of the folder that is storing the environment for your Rvr. Copy this because we will be using it in a second.

In your home folder you have a special file called _.profile_ and this file is ran everytime you log in for the first time. the . at the beginning of the folder name makes it a hidden folder and does not show up normally. This is an important folder so unless you know what you are doing don't change anything in the file that is not mentioned. At the end of the file add the following changing nothing else.
```bash
source /home/pi/.local/share/_NAME_OF_FOLDER_YOU_COPIED_HERE_/bin/activate
python ~/sphero-sdk-raspberrypi-python/Sparki/Python/set_start_up_led_color.py
exit
```
The first line executes the sphero environment so our program can run. The second line runs our program. THe last line exits the sphero environment. 

The next time you restart you Rvr with the pi attached it will change the colors of the LED's to whatever you make it.

# Bash Commands Used:
- __cd__: stands for Change Directory and is the command to move around your system from the command line / terminal
- __git clone__: copies a remote repository onto your computer
- __ls__: Lists the contents of a directory
- __mkdir__: makes a directory
- __touch__: creates a file or modifies the last time it was accessed

[connecting]: https://sdk.sphero.com/docs/getting_started/raspberry_pi/raspberry_pi_setup/#ssh-and-serial-port
[file]: https://github.com/benjaminmakowsky/Sparki/blob/master/Python/set_start_up_led_color.py