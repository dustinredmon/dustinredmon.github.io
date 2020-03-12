---
layout: post
title: Blog 18 - How to Create a LAMBDA function
---

This week for my senior design class, we are completeing AWS labs to prepare for our AWS certifications. One of the labs I did this week involved creating a LAMDA function. AWS Lambda is a serverless compute service that runs your code in response to events and automatically manages the underlying compute resources for you. LAMBDA can be quite useful for a number of reasons.  You can use AWS Lambda to extend other AWS services with custom logic, or create your own back-end services that operate at AWS scale, performance, and security. Anytime you need to compute something, it can be done with LAMBDA, and you will only be charged for the compute time used to run your code. 

AWS LAMBDA demonstrates a lot of key features. You can extend AWS services with custom logic with resources such as Amazon S3 buckets and Amazon DynamoDB tables, making it easy to apply compute to data as it is enters or moves through the cloud. You can also use AWS Lambda to create new back-end services for your applications that are triggered on-demand using the Lambda API or custom API endpoints built using Amazon API Gateway.

For this Lab, I needed to configure a scheduled event to trigger the Lambda function by setting a CloudWatch event as the event source. The Lambda function can be configured to operate much like a cron job on a Linux server, or a scheduled task on a Microsoft Windows server. However, you do not need to have a server running to host it.

So the first thing we want to do is choose LAMDA from the Services menu in the AWS Management Console. Then we are going to click "Create Function". In the create function screen, we want to configure these settings:

  - Choose "Author from scratch"
  - Function name: myStopinator
  - Runtine: Puthon 3.8
  - Click "Choose or create an execution role"
  - Execution role: Use an existing role
  - Existing role: From the dropdown list choose myStopinatorRole
  - Then click "Create Function"
  
Now we need to configure the trigger. Click add trigger. Click the Select a trigger dropdown menu, and choose CloudWatch Events. For the rule, choose Create a new rule and configure these settings:

  - Rule name: everyMinute
  - Rule type: Scheduled expression
  - Schedule expression: rate(1 minute)
  - then click "Add"
  
Now we need to configure the LAMBDA function. To do this we need to paste a few lines of code to update two values in the function code. In the Designer box, click myStopinator (which is the name of your Lambda function) to display and edit the Lambda function code. In the Function code box, delete the existing code. Copy the following code, and paste it in the box:

    import boto3
    region = '<REPLACE_WITH_REGION>'
    instances = ['<REPLACE_WITH_INSTANCE_ID>']
    ec2 = boto3.client('ec2', region_name=region)

    def lambda_handler(event, context):
        ec2.stop_instances(InstanceIds=instances)
        print('stopped your instances: ' + str(instances))
        
Replace the <REPLACE_WITH_REGION> placeholder with the actual Region that you are using. Copy the instance id for instance1 in the EC2 console. Return to the AWS Lambda console browser tab, and replace <REPLACE_WITH_INSTANCE_ID> with the actual instance ID that you just copied. Then in the top right corner, click save. 

You can now return to EC2 console to see if your LAMBDA function is running correctly. The LAMBDA function is programmed to stop instance1 at intervals of 1 minute.  Your instace should be stopped. If started back up, the LAMBDA function shoudl stop it again within 1 minute. 
