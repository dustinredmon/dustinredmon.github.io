---
layout: post
title: Blog 05 - Setup domain with AWS Route53
---

This week we are waiting on details and requirements for the next project, so I havent really worked on anything new. So I guess I will take this opportunity to go over something that I learned previously that I thought was really useful. In the last project I realized that it was easier to use an existing hosted zone for NS and SOA records. This avoids having to register the NS records with our domain provider everytime we run our Terraform code to create our infrastructure. To do that however, Terraform needs to be able to pull data from that existing zone (such as a Zone ID). This is where data sources come in to play. 

A data source is accessed via a special kind of resource known as a data resource, declared using a data block:

data "aws_route53_zone" "np-zone" {
  name = "novaprospekt.xyz"
}

A data block requests that Terraform read from a given data source ("aws_route_53_zone") and export the result under the given local name ("np-zone"). The name is used to refer to this resource from elsewhere in the same Terraform module, but has no significance outside of the scope of a module. Within the block body (between { and }) are query constraints defined by the data source. Here I have specified the hosted zone by the name of the zone ("novaprospekt.xyz"). This will pull data from the zone I have created named novaprospekt.xyz.

Now that we have created a data source, we can reference that data source by the name we have given it ("np-zone"):

resource "aws_route53_record" "www" {
  zone_id = "${data.aws_route53_zone.np-zone.zone_id}"
  name    = "www.novaprospekt.xyz"
  type    = "A"

  alias {
    name                   = "${aws_elb.np-elb.dns_name}"
    zone_id                = "${aws_elb.np-elb.zone_id}"
    evaluate_target_health = true
  }
}

Here we are creating a Route 53 record. To create this record we need the Zone ID of the hosted zone it needs to be created in. We get this information by referencing the data source we created ("${data.aws_route53_zone.np-zone.zone_id}"). This will pull the Zone ID from a Hosted Zone called "novaprospekt.xyz". We know this because we specified it in the data source. 

One of the most helpful things I learned when writing terraform is that you can use the Terraform manual to look up what attributes can be pulled from each data source. Just look up any data source in the manual, and you will see a list of attributes that can be pulled from it. 
