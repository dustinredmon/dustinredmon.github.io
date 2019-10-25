---
layout: post
title: Blog 07 - How to use AWS CLI with Terraform
---

This week I am starting a new AWS web server project that involves using Terraform to set up our infrastructure. One of the requirments of this project is that there shouldn't be any secrets stored in the terraform file. Typically the way Terraform works is that it uses AWS credentials to access your AWS account to make changes or create infrastructure. Normally we could just store these creditials in the terraform file like so:

    provider "aws" {
      access_key = "ACCESS_KEY_HERE"
      secret_key = "SECRET_KEY_HERE"
      region     = "us-east-1"
    }

But since I cannot store any secrets in the terraform file itself, I will be using AWS CLI to store my credentials on my local machine. The AWS Command Line Interface (CLI) is a unified tool to manage your AWS services. With just one tool to download and configure, you can control multiple AWS services from the command line and automate them through scripts.

So the first thing we want to do is install AWS CLI with the following command:

    sudo pip install awscli

You will need to install pip if you do not have it on your machine already. After you are finished installing AWS CLI, you will need to configure your credentials with the following command:

    aws configure

You will then be prompted to enter your AWS credentials to be stored on your local machine. Terraform is designed to work with AWS CLI, so this will allow you to run terraform code without storing your credentials in the terraform file itself. During this configuration setup you will prompted to enter you AWS Access Key ID, AWS Secret Access Key, Default Region Name:

    AWS Access Key ID [*****************WOLD]:
    AWS Secret Access Key [*****************6qBN]:
    Default Region Name [us-east-1]:
    
Information stored here is encrypted. The information presented in the brackets are the current credentials being stored in AWS CLI. These will be replaced when you enter new credentials.

Now that you have your credentials stored in AWS CLI, you can now run your Terrafrom code without entering your credentials in the files itself. AWS CLI will automatically retrieve your credentials for terraform to access your AWS account:

    provider "aws" {
      region     = "us-east-1"
    }
    
Notice that now you no longer need to store your credentails in the provider block as shown in the previous provider block example.
