---
layout: post
title: Blog 24 - How to logon a Private Server through a Bastion.
---

This week's blog is a bit of a stretch since I didn't really do any work his week. So please cut me some slack as it is the last week of my last semester of college. I decided that I would write about the one thing I keep on forgeting how to do every 6 weeks. And that is logging onto a private server through the bastion. I know it seems a little late to be covering this, but there are not many tasks about DevOps that troubles me. Whenever I need to do something, I plan it out in my head, and it all seems realitively simple to me. But when I sit down to do work on the server, I pause and think, "Wait, how do I log onto the private server again?". It's a stupid roadblock I hit everytime I want to do any work on a project. So I figured it would be best to write a blog about it so hopefully I can finally hammer it down in my head, so I can stop getting stuck on something so trivial everytime. This blog serves to be more helpful to me than anyone else. 

So in my case, I'm using windows. So I am going to need to download an SSH client to connect to my servers. The most common SSH client used on windows is Putty. Download putty, and dont forget to download the private key for your server. Keep this safe, as it cannot be created again. 

Putty cannot use your private key provided by AWS as is. PuTTY does not natively support the private key format for SSH keys. PuTTY provides a tool named PuTTYgen, which converts keys to the required format for PuTTY. You must convert your private key (.pem file) into this format (.ppk file) as follows in order to connect to your instance using PuTTY. Once you have converted your key you are ready to connect to your bastion.

Since I'm using Ubuntu on my instance, the format to connect to your instance in Putty is as follows:

    ubuntu@ec2-34-208-126-85.us-west-2.compute.amazonaws.com
    
This can very based on the AMI you are using.

Next. In the left navigation pane in Putty, select Conncetion>SSH>Auth. Look for "Private key file for Authentication:" at the bottom. Select the .ppk file you created earlier with PuTTYgen. Then click connect. This will connect you to the Bastion that should be available on your public subnet.

From here we can just ssh into our server on our private subnet, given that your server allows incoming connections from your bastion. All we need to do is enter the ssh command to connect to our server. Here is an example where I have the key stored locally:

    ssh -i "~/.ssh/keypair" ubuntu@10.0.4.187

