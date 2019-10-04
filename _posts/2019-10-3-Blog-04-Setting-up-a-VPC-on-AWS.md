---
layout: post
title: Blog 04 - Setting up a VPC on AWS
---
This week in my Senior Design class, we finally start working on or Senior Project. For this project we need to set up a Virtual Private Cloud. An AWS VPC allows any organization to setup one or more networks inside of AWS. The requirments for this project include the follwoing:
- 3 public subnets in 3 different availability zones (AZ) with at least 1024 available IP addresses per subnet.
- 3 private subnets in 3 different availability zones (AZ) with at least 4096 available IP addresses per subnet.
- 1 route table for the public subnets
- 1 route table for the private subnets
- 1 Internet Gateway (IG) attached to the default route of the public subnet routing table

So first we will open the VPC dashboard in AWS. Your AWS account comes with a default VPC installed. You can edit this VPC if you like, or delete it to start fro scratch. For this tutorial, I will be starting from scratch. Click on 'Your VPCs', and select 'Create VPC'. On this first page you can enter all the details of your VPC. Give your VPC a name. Enter an IP range in 'IPv4 CIDR block' such as 172.31.0.0/16. Leave everthing else as default, and click 'create'. Now you can see your VPC in your list of VPCs.

Next we need to create an Internet Gateway so that our instance can have access to the internet through the Amazon network. Without an Internet Gateway you will not be able to connect to the external world. So select 'Internet Gateways', and then select 'Create Gateway". Enter a name for your Internet Gateway, and click 'Create'. Right click on your new Internet Gateway and select 'Attach VPC'. Select the VPC you just created, and click "Attach".

Next we want to create our subnets. So click on 'Subnets', and then click 'Create subnet'. Enter a name for your subnet. Select your VPC. Select availibility Zone if you have a preference. Enter an IP range in 'IPv4 CIDR block'. This is how I decdided to divide our network for the 6 subnets I am going to create:

- public 1: 172.31.0.0/22
- public 2: 172.31.4.0/22
- public 3: 172.31.8.0/22
- private 1: 172.31.16.0/20
- private 2: 172.31.32.0/20
- private 3: 172.31.48.0/20

Next we want to create routing tables for our public and private networks. Select 'Route Tables' and click 'Create route table'. We will create our public route table first. So enter "Public RT" for the name and click 'Create'. Now open the 'Routes' tab and click 'Edit Routes'. Select 'Add route' and enter 0.0.0.0/0 as the Destination, and select the Internet Gateway as your target. This will allow incoming connections for your public subnets. Once you are done click ' Save routes'. Now select the 'Subnet Associations' tab and click 'edit subnet associations'. Here select all of the public subnets you created. Now that you are finished setting up the public route table, create a route table for your private subnet, but dont edit the routes tab. This is a private netwrok so it doesn't need access to the outside world.

Last but not least you should setup your security groups to accept connections from IPs that you trust. This is to prevent other people from trying to access your instances. By default, your VPC is set up to accept conenctions from any IP around the world. It is safer if you limit access to the PCs that you know are goning to be used. This is a little outside the scope of this tutorial, so I will save it for another time.
