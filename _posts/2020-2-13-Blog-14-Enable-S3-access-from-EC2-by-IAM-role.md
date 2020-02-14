---
layout: post
title: Blog 14 - Enable S3 access from EC2 by IAM role
---

This week for senior design class I have a simple lab. I need to create an S3 bucket and copy a file to it from an Ec2 instance. But before I can do that, I need to create an IAM role that will give my EC2 instance permission to access my S3 bucket. 

Identity and Access Management (IAM) is a very powerful utility to control the permissions of your AWS resources. This can be simplified into an equation such as:

    X is allowed to use Y
    
Typically "X" is a specific user, and "Y" is usually an AWS resource such as an Ec2 instance or s3 bucket. However, "X" doesnt have to be a specified user. X can also be a spicified AWS resource such as an EC2 instance we need to give permissions to. In this specific case we want to give an EC2 instance permission to access s3 buckets:

    “X” = our EC2 instances
    “Y” = S3 buckets
    
    therfore
    
    EC2 instance is allowed to use "S3 buckets"
    
X and Y can be interchanged with whatever resources you need to create. This may sound like a lot, but are just creating a simple rule for now. 

To create an IAM role, open the IAM console in AWS, select "Roles" in the left navigation pane, and click the "Create Roles" button. On this first page we want to choose what we want for “X” (which will be allowed to access “Y”). AWS calls it the “trusted entity”. Select EC2, of course. Click next. The second step on the next page is to choose “Y” which is the policy that will allow you access to a resource. Search for “S3” and then select “AmazonS3FullAccess”. This policy will allow full access to S3 buckets.

Finally, give this role a descriptive name. Here I use “full_S3_access_from_EC2”. (For the “Role description”, enter whatever you like or just keep default.)

Now a new IAM role is created. This only needs to be done once.

Whenever you launch a new EC2 instance, in “Step 3: Configure Instance Details”, select the IAM role you created previously for the “IAM role” option. Whenever you select this IAM role for an EC2 instance, it will have full access permission to your S3 buckets.
