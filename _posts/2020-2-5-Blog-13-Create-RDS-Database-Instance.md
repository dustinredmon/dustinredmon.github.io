---
layout: post
title: Blog 13 - Create an RDS Database Instance
---

It is the beginning of the second semester of my senior design class, and my group has been tasked with creating a database project for our senior design project. Not much work has been completed on the project so far. So I thought I would take some time this week to share what I have learned about creating databases in AWS.

Our project will most likely use a MySQL database to store customer accounts. In order to create a MySQL database, we need to create an RDS instance. An Amazon Relational Database Service (Amazon RDS) is a web service that that allows you to quickly create a relational database instance in the cloud. Amazon RDS manages the database instance on your behalf by performing backups, handling failover, and maintaining the database software.

Before creating an RDS instance it is important to have a VPC with both public and private subnets, and corresponding security groups. Additionally you might want to create a EC2 instance with a LAMP installation to connect to your database when finsihed.

The first step to creating an RDS instance is to log in to the AWS Management Console and open the Amazon RDS console. Remember to select what region you would like to use in the top-right corner. In the navigation pane, find "Databases" and select it. The click "Create Database". On the create page, you have the option to select "Standard Create" or "Easy Create". Select "Standard Create" and then select MySQL. 

In the templates section, select "Dev/Test". In the settings section, enter values for DC instance Identifier, Maser Username, auto-generate a password (disable), master password, and confirm password. In the DB instance size section, set the "DB instance performance type" to Burstable, and the "DB instance class" to db.t2.small. In the Storage and Availability & durability sections, use the default values. 

In the Connectivity section, you can set additional connection configuration. Select an existing VPC for "Virtual Private Cloud (VPC)". Select a Subnet group for the VPC. Set "Publicly accessible" to No. Choose an existing VPC security group that is configured for private access. Remove other security groups, such as the default security group, by choosing the X associated with each. "Availability zone" should be set to No Preference. And "Database port" should be set to 3306.

Next you can open the Additional configuration section, and enter sample for Initial database name. Keep the default settings for the other options. To finish creating your Amazon RDS MySQL DB instance, choose Create database. Your new DB instance now appears in the Databases list with the status "Creating". 

Wait for the Status of your new DB instance to show as Available. This will indicate when your database is finished being built. You can now click on the DB instance name to see its details. In the Connectivity & security section, you can view the Endpoint and Port of the DB instance you just created.

With this information you can now use an SQL client application or utility to connect to the instance. 


