---
layout: post
title: Blog 05 - Setup domain with AWS Route53
---

This week I finished work on my Apache web server project but setting up a domain for my website. I did this by using AWS Route53. Amazon Route 53 is a highly available and scalable cloud Domain Name System (DNS) web service. It is designed to give developers and businesses an extremely reliable and cost effective way to route end users to Internet applications by translating names like www.example.com into the numeric IP addresses like 192.0.2.1.

To start off, you need to purchase a domain name first. You can do this at any domain provider such a GoDaddy or SquareSpace. For this project I decided to go with NameCheap to be more cost effective. After you purchase your domain, you can open the Route53 console to begin your setup.

Inside the Route53 console, click 'Create Hosted Zone'. Enter the domain name you just purchase. For this project I purchased novaprospekt.xyz, so that is the domain name I will be entering. For your type, you can select either a 'public hosted zone' or a 'private hosted zone'. For this project I will be using a public hosted zone.

Next thing you want to do is take note of all of your NS records. These are your Name Server records. You will need to register these with your domain provider. This will allow visitors of your website to be redirected to your web server. 

The last thing we are going to do is create some A records to decide how our webserver will handle any incoming requests. Before you proceed any further you will need an EC2 instance up and running your webserver. You will also need a certificate if you want a secure connection. 

Enter the following A records into your record set:

    - www.novaprospekt.com      A         "EC2 IP" or "TLS certificate"
    - novaprospekt.com          A         www.novaprospekt.com
    - google.novaprospekt.com   CNAME     www.google.com

The first record is for wwww.novaprospekt.com. This will direct visitors to your webserver or TLS certificate. The second record is for users that do not enter the www before the website address. This will redirect them to www.novaprospekt.com. And the last is a CNAME record if you want to redirect users to another website.
