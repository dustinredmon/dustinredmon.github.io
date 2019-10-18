---
layout: post
title: Blog 06 - Create an SSL Certificate with AWS Certificate Manager (ACM)
---

For the last part of my webserver project, I will need to create an SSL certificate so that users will know that they have a secure connection when visiting our website. So the first thing we want to do is get a domain name if you havent already. Check out my previous blog if you need help setting up your domain in Route53.

Once you have your domain, open the Certificate Manager console in AWS. Click on the button that says "Request a Certificate". Here on the next page, you can select either a "public" or "private" certificate. You can create either one. For my webserver project I am going to use a public certificate. On the next page you will enter the domain name you are using. You can enter multiple domains if you like. After you are done adding domain, click 'next'.

On the next page you have two options for validating your domain. You can choose 'DNS validation' or 'Email validation'. If you have access to the emails associated with your domain, you can validate using the 'Email validation' option. If you don't have access to the emails asscoiated with the domain, then you will have to modify your DNS configuration to validate your domain. Validating your domain with 'DNS validation' is the easiest option. Especially if you used Route53 to setup your domain. What 'DNS Validation' does is it adds CNAME records to your Route53 configuration to verify that you are the owner. For this web server project, I will be using 'DNS Validation' cause I find it to be the easiest to do. On the next page, expand your certificate request. There will be an option to add CNAME records to your Route53 configuration. Complete this action to finish validating your certificate.

And that should be it! You know have a working certificate. You can now add certificate to your A records in Route53 so that visitors to your site will be redirected to the secure version of your site.
