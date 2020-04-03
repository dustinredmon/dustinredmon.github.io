---
layout: post
title: Blog 20 - Create an AMI for Autoscaling
---

This week for my senior design class, I had to work on a Lab that involved creating an AMI image to be used with Autoscaling services to automatically scale your infrastructure. In order to perform this task, you must already have your webserver running on an EC2 instance. What we are going to do is create an AMI from an existing web server. This will save the contents of the boot disk so that new instances can be launched with identical content.

First you want to open the AWS Management console and select EC2 from the services menu. Then click "Instances" in the navigation pane. Make sure that your instance is running. If it is not launch your instance and wait until the Status Checks for web server displays "2/2 checks passed. we are going to create an AMI based on this instance. Select the check box next to your web server instance. In the "Actions" menu, click "Image", and then "Create Image". Then configure:

    Image name: Web Server AMI
    Image description: Lab AMI for Web Server

The click "Create Image. The confirmation screen displays the AMI ID for your new AMI.

Now we can create a launch configuration for our Auto Scaling group. A launch configuration is a template that an Auto Scaling group uses to launch EC2 instances. When you create a launch configuration, you specify information for the instances such as the AMI, the instance type, a key pair, security group and disks. In the left navigation pane, click Launch Configurations. Click "Create launch configuration". In the left navigation pane, click My AMIs. We will be using the AMI that we created from our existing web server.

In the row for Web Server AMI, click "Select". Select the t3.micro instance type and click "Next: Configure details". Configure these settings:

Name: WebServerAMIConfig
Monitoring: Enable CloudWatch detailed monitoring

Next you will click "Next: Add Storage". Use the default storage settings, then click "Next: Configure Security Group". Here we will configure the launch configuration to use a Web Security Group. If you dont have a Web Security Group, you will need to create one first. Configure these settings:

    Click "Select an existing security group"
    Select  "Web Security Group"
    Click Review
    Review the details of your launch configuration and click "Create launch configuration"
    
You can ignore the warning "Improve security..." warning.

On the Select an existing key pair dialog:

    Select  I acknowledge...
    Click "Create launch configuration"
    
You will now create an Auto Scaling group that uses this Launch Configuration. Click "Create an Auto Scaling group using this launch configuration".

Configure the following settings:

    Group name: Lab Auto Scaling Group
    Group size: Start with: 2 instances
    Network: Lab VPC
     You can ignore the message regarding "No public IP address"
    Subnet: Select Private Subnet 1 (10.0.1.0/24) and Private Subnet 2 (10.0.3.0/24)
    This will launch EC2 instances in private subnets across both Availability Zones.

Expand  Advanced Details, then configure:

    Load Balancing: Select  Receive traffic from one or more load balancers
    Target Groups: LabGroup
    Monitoring: Select  Enable CloudWatch detailed monitoring
    
This will capture metrics at 1-minute intervals, which allows Auto Scaling to react quickly to changing usage patterns.

Click "Next: Configure scaling policies".Select "Use scaling policies to adjust the capacity of this group." Modify the Scale between text boxes to scale between 2 and 6 instances. This will allow Auto Scaling to automatically add/remove instances, always keeping between 2 and 6 instances running.

In Scale Group Size, configure:

    Metric type: Average CPU Utilization
    Target value: 60
    
This tells Auto Scaling to maintain an average CPU utilization across all instances at 60%. Auto Scaling will automatically add or remove capacity as required to keep the metric at, or close to, the specified target value. It adjusts to fluctuations in the metric due to a fluctuating load pattern.

Click "Next: Configure Notifications". Auto Scaling can send a notification when a scaling event takes place. You will use the default settings. 

Click "Next: Configure Tags". Tags applied to the Auto Scaling group will be automatically propagated to the instances that are launched. Configure the following:

    Key: Name
    Value: Lab Instance

Click "Review". Review the details of your Auto Scaling group, then click "Create Auto Scaling group". If you encounter an error "Failed to create Auto Scaling group", then click Retry Failed Tasks.

Click Close when your Auto Scaling group has been created. Your Auto Scaling group will initially show an instance count of zero, but new instances will be launched to reach the Desired count of 2 instances.

You have now created an Auto Scaling group with a minimum of two instances and a maximum of six instances. Currently two instances are running because the minimum size is two and the group is currently not under any load. If the load were to increase, your infrastructure now has the ability to scale its instances based on the traffic load.




