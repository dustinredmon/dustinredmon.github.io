---
layout: post
title: Blog 23 - Create IAM SES User with Terraform (SES Setup Part.3)
---

This week I continue working on my senior design project. As I have mentioned before in previous blog posts, I am working on an Amazon Simple Email Service implementation for my login app. This blog will be part 3 of my SES setup up turorial. In previous blogs, I discussed how to generate SMTP credentials, and how to verify emails for the sender and receiver. In this tutorial, I will discuss how to genertate SMTP credentials by creating an IAM user with SES privlages using Terraform.

The first thing we want to do is is create an IAM user:

    resource "aws_iam_user" "ses-smtp" {
      name = "ses-smtp-user"
    }

Next we can create the policy for the user to allow SES privlages:

    resource "aws_iam_user_policy" "ses-smtp" {
      name = "AmazonSesSendingAccess"
      user = aws_iam_user.ses-smtp.name
      policy = <<EOF
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
    EOF
    }
    
Last but not least, we need  to generate an access key to use as the SMTP credentials:
 
    resource "aws_iam_access_key" "ses-smtp" {
      user    = aws_iam_user.ses-smtp.name
      pgp_key = var.pgp_key
    }
 
These three blocks of code, will create an IAM user, apply a policy to allow access to SES, and generate SMTP credentials for that user. The only thing left to do is extract the password to be used for the SMTP credentials. This can be done by pulling the "ses_smtp_password_v4" attribute associated with the "aws_iam_access_key" you just created:

    output "aws_iam_smtp_password_v4" {
      value = aws_iam_access_key.ses-smtp.ses_smtp_password_v4
    }

Please note that you should avoid using the attribute "aws_iam_smtp_password" has depricated, and the current version, "aws_iam_smtp_password_v4" should be used instead. Support for generating SES SMTP v4 passwords into a new ses_smtp_password_v4 attribute has been merged and will release with version 2.50.0 of the Terraform AWS Provider. 

It is also worth noting that secret access key converted into an SES SMTP password by applying AWS's documented Sigv4 conversion algorithm is region specific. Valid Provider regions are ap-south-1, ap-southeast-2, eu-central-1, eu-west-1, us-east-1 and us-west-2.
