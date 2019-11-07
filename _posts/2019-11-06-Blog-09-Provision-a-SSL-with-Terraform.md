---
layout: post
title: Blog 09 - How to provision a SSL certificate with Terraform
---

This week I continue work on my web server project using Terraform. Today I will be focusing on provisioning an SSL certificate in ACM using Terraform. Amazon Certificate Manager (ACM) makes the process of provisioning, validating, and configuring Transport Layer Security (TLS) certificates very easy. When combined with Terraform, that process gets woven directly into your infrastructure configuration in a way that’ll leave you never wanting to provision ACM certificates through the console again.

Normally when creating a certificate through in the console we need to create a certificate and validate it through DNS records. We will do the exact same thing but with Terraform. To create and validate our certificate we will need the following three Terraform resources:

  - aws_acm_certificate: To request a certificate for example.com
  - aws_route53_record: To create a DNS record to validate the certificate request
  - aws_certificate_validation: To ensure that ACM validates our DNS record before certificate use

First, define a resource for the domain ACM certificate and set its validation method to use DNS:

resource "aws_acm_certificate" "default" {
  domain_name       = "example.com"
  validation_method = "DNS"
}
This will create the actual certificate, but it will not be ready to use until we validate it first. In order to validate the certificate, we use the outputs of aws_acm_certificate to create a Route 53 DNS record to confirm domain ownership:

data "aws_route53_zone" "external" {
  name = "example.com"
}
resource "aws_route53_record" "validation" {
  name    = "${aws_acm_certificate.default.domain_validation_options.0.resource_record_name}"
  type    = "${aws_acm_certificate.default.domain_validation_options.0.resource_record_type}"
  zone_id = "${data.aws_route53_zone.external.zone_id}"
  records = ["${aws_acm_certificate.default.domain_validation_options.0.resource_record_value}"]
  ttl     = "60"
}

Here we are using the data from a pre-existing zone, but you could easily create a new zone with AWS_route53_zone resource, but you would ne re-register your NS records with your Domain provider.

After that, use the aws_acm_certificate_validation resource to wait for the newly created certificate to become valid:

resource "aws_acm_certificate_validation" "default" {
  certificate_arn = "${aws_acm_certificate.default.arn}"
  validation_record_fqdns = [
    "${aws_route53_record.validation.fqdn}",
  ]
}

Once that is completed, you should have been issued a new verified SSL certificate that has been validated. You can reference the certificate using its ARN:

acm_certificate_arn      = "${aws_acm_certificate_validation.default.certificate_arn}"

Here is an example of how I use my certificate on a listener for my ELB:

listener {
    instance_port      = 80
    instance_protocol  = "http"
    lb_port            = 443
    lb_protocol        = "https"
    ssl_certificate_id = "${aws_acm_certificate_validation.default.certificate_arn}"
  }

And that is everything you need to know about creating an SSL cetificate with Terraform.
