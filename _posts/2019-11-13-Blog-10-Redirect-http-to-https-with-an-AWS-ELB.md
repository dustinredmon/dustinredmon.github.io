---
layout: post
title: Blog 10 - Redirect HTTP to HTTPS with an ELB
---

This week I continued work on my web server project using Terraform. One of the requirements of this project is to redirect http traffic to an https domain. I thought this would be a simple task that could be configured in Terrafom. Then I soon realized that Elastic Load Balancers, or Classic Load Balancers, do not redirect traffic from http to https, only an Application Load Balancer can. Considering that my project is near completion, I was terrified of starting all over with a new Application Load Balancer. So I have found a way to redirect traffic by configuring the apache files on the server.

Classic Load Balancers can't redirect HTTP traffic to HTTPS. Instead, you must include rewrite rules on the web servers of Amazon Elastic Compute Cloud (Amazon EC2) instances behind your Classic Load Balancer. You must configure your rewrite rules to use the X-Forwarded-Proto header and redirect only HTTP clients. If you don't, the rewrite rules can create an infinite loop of redirection requests between your Classic Load Balancer and the instances behind it. Such a loop results in the error ERR_TOO_MANY_REDIRECTS.

In order to accomplish this we must configure our web servers behind our Classic Load Balancer to use the X-Forwarded-Proto header to direct traffic based on whether clients use HTTP or HTTPS.It is important that we rewrite rules to your web servers that redirect clients using HTTP to an HTTPS URL, and serve clients using HTTPS directly.

Here is how to configure Apache servers tp use the X-Forwarded-Proto header:

1.    Open your Apache configuration file. Possible locations include /etc/httpd/conf/httpd.conf (Apache 2/httpd), /etc/apache2/sites-enabled/ (Apache 2.4), or /etc/apache2/apache2.conf (Apache on Ubuntu).

2.    Add a rewrite rule to the VirtualHost section of your configuration file similar to the following:
    <VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTP:X-Forwarded-Proto} =http
    RewriteRule .* https://%{HTTP:Host}%{REQUEST_URI} [L,R=permanent]
    </VirtualHost>

3.    Save your Apache configuration file.

4.    Restart Apache.

Now all of your http requests will be redirected to https.
