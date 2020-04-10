---
layout: post
title: Blog 21 - Create an Amazon RDS DB Instance with High Availability
---

This week for my senior design class I am working on a lab that involves building a DB Server and interacting with the BD using an App. So for the blog this week I thought I would go over the process of launching an Amazon RDS DB instance with high availability.

The first thing we want to do is create a security gorup for our  RDS DB instance. This security group will be used when you launch your database instance. So in the AWS Management Console, select "VPC" from "Services" menu. Then in the navigation pane, click "Security Groups". Click the button that says "Create Security Group" and then use the following configuration:

    Security group name: DB Security Group
    Description: Permit access from Web Security Group
    VPC: Lab VPC

Click the button that says "Create", then click "Close".

Next we will will ass a rule to the security gorup that will allow incoming database requests. Select the "DB Security Group", and click the "Inbound Rules" tab. Since the security group has no rules, we will need to add a rule to permit access from the Web Security Group. So click "Edit Rules", then click "Add rule" then use the following configuration:

    Type: MySQL/Aurora (3306)
    CIDR, IP, Security Group or Prefix List: Type sg and then select Web Security Group

This configures the Database security group to permit inbound traffic on port 3306 from any EC2 instance that is associated with the Web Security Group. Click "Save Rules", then click "Close". We will use this security group when launching the Amazon RDS database.

Next we need to create a DB Subnet Group that is used to tell RDS which subnets can be used for the database. Each DB subnet group requires subnets in at least two Availability Zones. On the Services  menu, click RDS. In the left navigation pane, click Subnet groups. Click Create DB Subnet Group then configure:

    Name: DB Subnet Group
    Description: DB Subnet Group
    VPC: Lab VPC
    Availability zone: Select the first Availability Zone
    Subnet: 10.0.1.0/24
    Click "Add subnet"
    This added Private Subnet 1. You will now add Private Subnet 2.
    
Configure these settings (on the existing screen):

    Availability zone: Select the second Availability Zone
    Subnet: 10.0.3.0/24
    Click "Add subnet"
    These subnets should now be shown in the list: 10.0.1.0/24 and 10.0.3.0/24
    
Click Create. We will use this DB subnet group when creating our database.

So now we can finally create an Amazon RDS DB Instance. For this task, we will configure and launch a Multi-AZ Amazon RDS for MySQL database instance. Amazon RDS Multi-AZ deployments provide enhanced availability and durability for Database (DB) instances, making them a natural fit for production database workloads. When you provision a Multi-AZ DB instance, Amazon RDS automatically creates a primary DB instance and synchronously replicates the data to a standby instance in a different Availability Zone (AZ).

In the left navigation pane, click Databases. Click Create database. Select  MySQL. Under Settings, configure:

    DB instance identifier: lab-db
    Master username: master
    Master password: lab-password
    Confirm password: lab-password
    
Under DB instance size, configure:

    Select  Burstable classes (includes t classes).
    Select db.t3.micro
    
Under Storage, configure:

    Storage type: General Purpose (SSD)
    Allocated storage: 20

Under Connectivity, configure:

    Virtual Private Cloud (VPC): Lab VPC

Expand  Additional connectivity configuration, then configure:

    For Existing VPC security groups: click DB Security Group to highlight it in blue.
  
Expand  Additional configuration, then configure:

    Initial database name: lab
    Uncheck Enable automatic backups.
    Uncheck Enable Enhanced monitoring.

This will turn off backups, which is not normally recommended, but will make the database deploy faster for this lab. Click Create database. Your database will now be launched.

Click lab-db (click the link itself). You will now need to wait approximately 4 minutes for the database to be available. The deployment process is deploying a database in two different Availability zones.
