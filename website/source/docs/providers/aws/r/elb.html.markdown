---
layout: "aws"
page_title: "AWS: aws_elb"
sidebar_current: "docs-aws-resource-elb"
description: |-
  Provides an Elastic Load Balancer resource.
---

# aws\_elb

Provides an Elastic Load Balancer resource.

## Example Usage

```
# Create a new load balancer
resource "aws_elb" "bar" {
  name = "foobar-terraform-elb"
  availability_zones = ["us-west-2a", "us-west-2b", "us-west-2c"]

  listener {
    instance_port = 8000
    instance_protocol = "http"
    lb_port = 80
    lb_protocol = "http"
  }

  listener {
    instance_port = 8000
    instance_protocol = "http"
    lb_port = 443
    lb_protocol = "https"
    ssl_certificate_id = "arn:aws:iam::123456789012:server-certificate/certName"
  }

  health_check {
    healthy_threshold = 2
    unhealthy_threshold = 2
    timeout = 3
    target = "HTTP:8000/"
    interval = 30
  }

  instances = ["${aws_instance.foo.id}"]
  cross_zone_load_balancing = true
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) The name of the ELB
* `availability_zones` - (Required for an EC2-classic ELB) The AZ's to serve traffic in.
* `security_groups` - (Optional) A list of security group IDs to assign to the ELB.
* `subnets` - (Required for a VPC ELB) A list of subnet IDs to attach to the ELB.
* `instances` - (Optional) A list of instance ids to place in the ELB pool.
* `internal` - (Optional) If true, ELB will be an internal ELB.
* `listener` - (Required) A list of listener blocks. Listeners documented below.
* `health_check` - (Optional) A health_check block. Health Check documented below.
* `cross_zone_load_balancing` - (Optional) Enable cross-zone load balancing.

Exactly one of `availability_zones` or `subnets` must be specified: this
determines if the ELB exists in a VPC or in EC2-classic.

Listeners support the following:

* `instance_port` - (Required) The port on the instance to route to
* `instance_protocol` - (Required) The the protocol to use to the instance.
* `lb_port` - (Required) The port to listen on for the load balancer
* `lb_protocol` - (Required) The protocol to listen on.
* `ssl_certificate_id` - (Optional) The id of an SSL certificate you have uploaded to AWS IAM.

Health Check supports the following:

* `healthy_threshold` - (Required) The number of checks before the instance is declared healthy.
* `unhealthy_threshold` - (Required) The number of checks before the instance is declared unhealthy.
* `target` - (Required) The target of the check.
* `interval` - (Required) The interval between checks.
* `timeout` - (Required) The length of time before the check times out.

## Attributes Reference

The following attributes are exported:

* `id` - The name of the ELB
* `name` - The name of the ELB
* `dns_name` - The DNS name of the ELB
* `instances` - The list of instances in the ELB
