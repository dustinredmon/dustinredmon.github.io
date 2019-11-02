---
layout: post
title: Blog 08 - How to create an ELB with Terraform
---

This week I am continuing to work on my web server project which invlolves using Terraform to implement infrastructure. The task I have been assigned to do is create a load balancer to direct incoming traffic to our private servers to access the weather app we have created. So for this blog I will be discussing for to create a load balancer using Terraform. I have decided to use an Elastic Load Balancer instead of an Application Load Balancer, because I am more familiar with AWS's Classic Load Balancer.

Here is an example of a typical load balancer:

resource "aws_elb" "np-elb" {
  name = "np-elb"
  subnets = ["${aws_subnet.main-1-public.id}", "${aws_subnet.main-2-public.id}"]
  security_groups = ["${aws_security_group.elb-securitygroup.id}"]
  listener {
    instance_port = 80
    instance_protocol = "http"
    lb_port = 80
    lb_protocol = "http"
  }
  health_check {
    healthy_threshold = 2
    unhealthy_threshold = 2
    timeout = 3
    target = "HTTP:80/"
    interval = 30
  }
  
  instances = ["${aws_instance.server-1.id}", "${aws_instance.server-2.id}"]
  cross_zone_load_balancing = true
  connection_draining = true
  connection_draining_timeout = 400
  tags {
    Name = "my-elb"
  }
}

Our Load Balancer needs to be inside a public subnet. It is a good idea to make it available in two different subnets in two different availability zone in case on subnet becomes unavailable, visitors will still be able to access your website. So under subnets, we want to list our two public subnets. 

    subnets = ["${aws_subnet.main-1-public.id}", "${aws_subnet.main-2-public.id}"]
    
The next thing we want to do is look at instances. Here I have listed the two instances that are running my app inside a public subnet. This is optional, cause you can alternatively attach this elb to an autoscaling group to usre/create instances.

    instances = ["${aws_instance.server-1.id}", "${aws_instance.server-2.id}"]
    
Next we want to look at the listener. Here I have created a listener for port 80. This will forward any incoming traffic on port 80 to our private servers we have listed. You can also add another listner for port 443 for https.

We also need a health checks to make sure that we have  instances running and that they can accepts incoming traffic.

    health_check {
      healthy_threshold = 2
      unhealthy_threshold = 2
      timeout = 3
      target = "HTTP:80/"
      interval = 30
    }
    
And the last thing we need to look at is the security group.

    security_groups = ["${aws_security_group.elb-securitygroup.id}"]
    
We need to create this security group so that it will allow incoming connections from the outside world.

resource "aws_security_group" "main-sg" {
  name   = "bastion-sg"
  vpc_id = "${aws_vpc.main_vpc.id}"

  ingress {
    protocol    = "tcp"
    from_port   = 22
    to_port     = 22
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  ingress {
    from_port = 80
    to_port = 80
    protocol = "tcp"
    security_groups = ["${aws_security_group.elb-securitygroup.id}"]

  egress {
    protocol    = -1
    from_port   = 0 
    to_port     = 0 
    cidr_blocks = ["0.0.0.0/0"]
  }
}
	
resource "aws_security_group" "elb-securitygroup" {
  vpc_id = "${aws_vpc.main_vpc.id}"
  name "elb"
  description = "security group for load balancer"
  egress {
    from_port   = 0 
    to_port     = 0 
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

In my security group terrafrom file I have added a security group for the ELB. I also added an ingress for port 80 in the main-sg to send incoming traffic to the elb-sg.

Now with all of this setup you can now test your website on the private server. If you do not have a website ready, you can install apache and use the default page to test if your elb is working.
    
