---
layout: post
title: Blog 01 - Dual Booting Windows and Linux
---
After dealing with several frustrations reagarding virtual machines this week, I have decided I am finally going to configure a dual-boot OS configuration for my laptop. I was working on my Docker lab for this week, and everything was going smoothly until my VM in Oracle VirtualBox just decided to have a mind of i'ts own and did not want to follow any of the commands I was giving it. I didn't find Docker very complicated to use, which made these issues even more frustrating. Having been down this road before with VMs, I decided enough is enough. I am finally going to take the dive into a Dual-boot OS system. I am so excited!

So first things first, we should establish a few pre-requisites. To start off you are going to need:
  - a computer with Windows pre-installed (with enough free space for your Ubuntu partition)
  - a good internet connection to download linux and the required software
  - a USB disk with at least 2GB of space

Next you want download all the software you are going to need. We need to download Ubuntu 18.04 from the Ubuntu website. We are also going to download Etcher from Etcher.io to create a Live USB to install our dual-boot partition. 

Next we want to format our USB and create a partition on our windows machine to install Linux. We want to format our USB to Fat32. This is important for it to work with Live USB. To create a partition for our Linux installation we must open up Disk Management on our Windows machine. Right click on the partition for the C: drive, and select shrink volume. Here you can enter how much space you want to free up for you Linux partition. 

After you have everything prepared, you can now use Etcher to create a Live USB to install your Linux partion. Just open Etcher, select your USB and you iso image for you Linux installation, and Etcher will automtically create the Live USB for you. 

From here you are ready to start your installation. Restart your PC with the Live USB inserted and keep pressing F10 or F12 to launch the boot menu. When prompted select the option to Install Ubuntu. From here everything is straight forward. Follow the directions on-screen to complete your installation. Congratulations! The next time you restart your PC you will be given to launch your Ubuntu or Windows operating system!!

