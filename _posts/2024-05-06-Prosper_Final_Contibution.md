---
layout: post
# If your post title is longer or more complicated
# than can be represented in the filename, uncomment the following line
# and specify a custom title
title:  "Bug Fix Contribution: Electric Vehicle Team"

# Uncomment only one of the below categories
categories: 
- Bug Fix
#- Contribution


# Enter your name below
author: Prosper Omiponle
---

![EVT Logo](../assets/2024-04-21-Prosper_Contribution/foss_EVT.webp)
<br>

In my last blog post, I mentioned how being very early in my career, my personal experience has been characterized by a need to prove myself. In the Spring of 2023, a good chunk of my time was spent working on the RIT Electric Vehicle Team, and my previous blog post highlighted my very first pull request in the team's repo. It was pretty small, but it introduced me to the workflow on the team and helped me get going with tackling bigger tasks on the team.
In this entry, I'll dive into one of (if not the) greatest contribution I've made to the team thus far, as it actually ended up being used on the final electric bike (!); the REV1 (Road Electric Vehicle mark 1).

![Figure 1](../assets/2024-04-21-Prosper_Contribution/foss_Fig1.webp)
<br>
Figure 1: Summary clip of Final Contribution

 I chose this project having worked on the team for a while and possesing a pre-existing knowledge of the codebase. I didn't use my Comm Arch experience to determine whether it would be good to work with, however, I ran Caudron scan on the overall repo which I displayed in my previous post, and it is included here again, showing that the activity (during the school year since it is a school project) is pretty high and fairly stable.

![Figure 2](../assets/2024-04-21-Prosper_Contribution/foss_Fig2.webp)
<br>
Figure 2: Cauldron stat for RIT-EVT

## The Issue
Our I2C's `readReg()` method wasn't reading multi-byte registers correctly. When reading more than 1 byte from a register, the STM (the primary microcontroller used in this project) basically seemed to read a number of individual bytes with no ACK between them. An ACK (which is short for acknowledgement) designates the start and end of different bytes in the data stream.

## The Contribution
I created a new branch off of EVT main and began gathering information. Being my first real team contribution, the team lead took it upon himself to thoroughly explain how the technology I2C works. I also did some research on it to see how I would apply that knowledge to fix the issue. 

Because it was an issue involving using the same method to do slightly different things with different parameters, this was a textbook case of a need to implement polymorphism.

![Figure 3](../assets/2024-04-21-Prosper_Contribution/foss_Fig3.webp)
<br>
Figure 3: `readReg()` method with a bug in it

The code above shows the original state of the `readReg()` method, which made use of loops attempting to use the length of the register, regLength, which is passed in as a parameter, as a limit for when to stop reading the bytes. However, the ACK is already supposed to do the job of designating individual bytes.

By referencing the HAL (Hardware Abstraction Layer -which is pretty much what python is to raw C: wrapping functionality in slightly more human readable formats, just specific to the hardware being used in this case) documentation and reading the code itself, I found that the `write()` and `read()` methods take care of the ACKs within their implementation, so I got rid of the loops, and it functioned as expected. This was where the polymorphism was already implemented, so it just needed to be called by passing in the right number of parameters - which in this case was done by adding "length".

![Figure 4](../assets/2024-04-21-Prosper_Contribution/foss_Fig4.webp)
<br>
Figure 4: Fixed method

## The Merge
Merging the change wasn't too much of a hassle, however, like any decently collaborative team normally should have, the team has a policy that 3 people must review the pull request before it can be merged. This was quickly achieved as the functionality could be verified on anybody's local machine.

## Conclusion

This was a good introductory and beginner friendly bug to work on and fix in my early days as an Electric Vehicle Team member. It gives a glimpse into the sort of things one might have to be mindful of when developing embedded software for multiple ECUs (Electrically Controlled Units) because of the nature of the elements which calls for reusability when possible. It is important to seek help when lost and listen attentively to gain insights on whatever you are working on, because it is quite literally the light with which we see the way toward making any progress at all!
