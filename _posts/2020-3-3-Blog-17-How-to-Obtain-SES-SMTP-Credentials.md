---
layout: post
title: Blog 17 - How to Obtain SES SMTP Credentials
---

This week, I continue work on the Login Page app for my Senior Design class. I have been tasked with configuring the AWS Simple Email Service for our app. Our login app uses SMTP protocols to sends emails. You can send email through Amazon SES by using applications or programming languages that use the SMTP protocol. So I will need to generate SMTP credentials for SES so that our app can send emails on the new email service. 

We can generate credentials manually by logging into AWS, and opening the SES console. Select SMTP settings in the navigation pane on the left. This will display a lost of information about your SES mail service such as server names, and port numbers. You will also see a button that says "Generate SMTP credentials". To send email using the Amazon SES SMTP interface, you first have to create a set of SMTP credentials. Your SMTP credentials are the user name and password that you use to connect to an Amazon SES SMTP endpoint. SMTP credentials are unique to each AWS Region. If you want to send email in more than one Region, you have to generate a unique set of credentials for each Region. To obtain SMTP credentials for the current Region, choose Create My SMTP Credentials.

Generating credentials through the SES console is pretty simple, but from a DevOps perspective we want to be able to automate this process. When you generate SMTP credentials by using the Amazon SES console, the Amazon SES console creates an IAM user with the appropriate policies to call Amazon SES and provides you with the SMTP credentials associated with that user. So in order to automate this process we will have to create an IAM user with the correct permissions and policies. 

To enable the IAM user to send email using the Amazon SES SMTP interface, you need to do the following two steps:

- Derive the user's SMTP credentials from their AWS credentials. The SMTP username is the same as the AWS access key ID, so you just need to generate the SMTP password. Amazon provides an algorithim to derive your password credentials, but lucky for us, Terraform has an attribute that can output your SES SMTP credententials unencrypted.

- Apply the following policy to the IAM user:

    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ses:SendRawEmail",
                "Resource": "*"
            }
        ]
    }
    
Here is an example in terraform:

    resource "aws_iam_user" "this" {
      count                = var.enabled ? 1 : 0
      name                 = var.user_name
      path                 = var.path
      permissions_boundary = var.permissions_boundary
      force_destroy        = var.force_destroy
      tags                 = var.tags
    }

    resource "aws_iam_access_key" "this" {
      count   = var.enabled ? 1 : 0
      user    = aws_iam_user.this[0].name
      pgp_key = var.pgp_key
    }

    data "aws_iam_policy_document" "ses_send_access" {
      statement {
        effect = "Allow"

        actions = [
          "ses:SendRawEmail",
        ]

        resources = ["*"]
      }
    }

    resource "aws_iam_user_policy" "this" {
      count       = var.enabled ? 1 : 0
      name_prefix = var.user_policy_name_prefix
      user        = aws_iam_user.this[0].name

      policy = data.aws_iam_policy_document.ses_send_access.json
    }

    output "this_user_name" {
      value       = element(concat(aws_iam_user.this.*.name, [""]), 0)
      description = "IAM user name"
    }

    output "this_user_arn" {
      value       = element(concat(aws_iam_user.this.*.arn, [""]), 0)
      description = "ARN of the IAM user"
    }

    output "this_access_key" {
      value       = element(concat(aws_iam_access_key.this.*.id, [""]), 0)
      description = "IAM Access Key of the created user, used as the STMP user name"
    }

    output "this_ses_smtp_password" {
      value       = element(concat(aws_iam_access_key.this.*.ses_smtp_password, [""]), 0)
      description = "The secret access key converted into an SES SMTP password"
      sensitive   = true
    }

