---
layout: post
title: Blog 19 - Deploy an Application to Elastic Beanstalk
---

This week for my senior design class, we continue to complete AWS labs to prepare for our AWS certifications. One of the labs I did this week involved deploying a sample application into an Elastic Beanstalk. AWS Elastic Beanstalk is an easy-to-use service for deploying and scaling web applications and services developed with Java, .NET, PHP, Node.js, Python, Ruby, Go, and Docker on familiar servers such as Apache, Nginx, Passenger, and IIS. Once you upload your code, Elastic Beanstalk will automatically handle all of the deployment from capacity provisioning, load balancing, auto-scaling, and application health monitoring. With Elastic Beanstalk, there isn't any additional chrage. You only pay for the AWS resources needed to store and run your applications.

So the first thing we want to do is create an Elastic Beanstalk. In the AWS Management Console, from the Services menu, choose Elastic Beanstalk. Then click "Get Started" to start creating your Elastic Beanstalk. On this next page, you want to create a name for your beanstalk. Then you want to select a platform. I will be choosing "Tomcat" since I will be using a Tomcat app. Lastly I will tick the box that says "Use sample Application" instead of "upload code". We will upload code later. It will take a few minutes for your Elastic Beanstalk to be created, so be patient. Once the box for your Elastic Beanstalk turns green in color, it is ready to be used.

In the green application details box, click the URL value that is displayed. The Dashboard page for your Elastic Beanstalk environment shold open. Notice that the page shows that the health of your application is green (good). The Elastic Beanstalk environment is ready to host an application. However, it does not yet have running code. Near the top of the page, click the URL (the URL ends in elasticbeanstalk.com). When you click the URL, a new browser tab will open up, however it will display a page saying "HTTP Status 404 - Not Found". This is because this application server doesn't have an application running on it yet. 

Next, I will use a sample application in order to demonstrate how to deploy it onto the Elastic Beanstalk. You can downolad the same sample appliation at: https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/samples/java-tomcat-v3.zip 

Back in the Elastic Beanstalk console, click Upload and Deploy. Click Browse or Choose File, then navigate to and open the java-tomcat-v3.zip file that you just downloaded. Click Deploy. It will take a minute or two for Elastic Beanstalk to update your environment and deploy the application. After the deployment is complete, reload the browser tab that displayed the 404 status and the web application that you deployed should be displayed.

If you return to theElastic Beanstalk console and click Configuration, here you can examine all the details and configurations for your Elastic Beanstalk. You can monitor your instances and create databases for your app very easily. 



