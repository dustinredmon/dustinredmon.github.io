---
layout: post
title: Blog 16 - Using Modules in Terraform
---

Sometimes it it best to keep things simple and not try to reinventing the wheel. The same can be said with writing code. It is usually not worth the frustration to do create something from scratch that was been done a million times before you by way more qualified people. If we had to start from the beginning everytime we tried to do something, we would never make it far or push ourselves to learn or achieve more complicated tasks. That is why Terraform allows you to use modules to incorporate abstract tasks into your code without having to write the code yourself. This is especially useful when you are try to schieve something that you know has been done before.

A perfect example for me is that I am trying to create an IAM user to access the SES email sever for my current senior design project. In order to automate everything, I need to pull the credentials for that IAM user so that they can be used by the app without having to manually enter the credentials. Typicallly those credentials are encrypted, so they need to be decrypted as well. All of this sounds doable, but I can save myself a lot of time and frustration if I could just find a module that can create that IAM user, and automatically pull those credentials for me. Something of this nature is likely to exist since, thousands of people beofre me must have tried to achieve the same outcome. So if I can find a module that I can use, I can reuse that code by create a reference to the source in my terraform code. 

Here is an example of how I can reference the module I have found for creating an IAM user and pulling credentials:

    module "iamcredentials" {
    source = "github.com/Flaconi/terraform-aws-iam-ses-user"
    }
    
The above address scheme will clone over HTTPS. To clone over SSH, use the following form:

    module "iamcredentials" {
    source = "git@github.com:Flaconi/terraform-aws-iam-ses-user.git"
    }
    
