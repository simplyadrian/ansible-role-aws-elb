#ansible-role-elb
=========

Manage AWS ELB.

Requirements
------------

- Ansible 2.0.1 or higher.

Role Variables
--------------

| parameter             | required | default | choices | comments |
| --------------------- | -------- | ------- | -------- |-------- |
| aws_elb_subnets | yes| | | A list of VPC subnets to use when creating ELB. Zones should be empty if using this. |
| aws_elb_scheme| no| internet-facing | internet-facing, internal|The scheme to use when creating the ELB. For a private VPC-visible ELB use 'internal'. |
| aws_elb_security_group_ids| | | |A list of security groups to apply to the elb |
| aws_elb_instance_listeners| | | | List of ports/protocols for this ELB to listen on (see [vars](defaults/main.yml)| 
| aws_elb_ping_path| no | / | |The destination for the HTTP or HTTPS request. | 
| aws_elb_healthcheck_response_timeout|yes | | | The amount of time to wait when receiving a response from the health check, in seconds.| 
| aws_elb_ping_protocol | no| http| | The protocol to use to connect with the instance. Ping protocols: TCP, HTTP, HTTPS, and SSL|
| aws_elb_ping_port | no| http| | The port to use to connect with the instance, as a protocol:port pair. If the load balancer fails to connect with the instance at the specified port within the configured response timeout period, the instance is considered unhealthy.|
| aws_elb_healthcheck_interval| no | 10 | |The amount of time between health checks of an individual instance, in seconds. | 
| aws_elb_unhealthy_threshold| no | 5 | |The number of consecutive failed health checks that must occur before declaring an EC2 instance unhealthy. | 
| aws_elb_healthy_threshold | | no | 3| The number of consecutive successful health checks that must occur before declaring an EC2 instance healthy.| 
| aws_elb_cross_az_load_balancing| no | yes|yes, no | Distribute load across all configured Availability Zones | 
| aws_elb_draining_timeout| no |20 | | Wait a specified timeout allowing connections to drain before terminating an instance| 
| aws_resource_tags  | yes  |   | | a hash/dictionary of tags to add to the new instance or for starting/stopping instance by tag; '{"key":"value"}'|
| aws_wait_timeout | no | 600 | |  how long before wait gives up, in seconds | 
| state |  no |  present |present, absent| create or destroy elb  |
| region |  yes |   || The AWS region to use. Must be specified if ec2_url is not used. If not specified then the value of the EC2_REGION environment variable, if any, is used. See http://docs.aws.amazon.com/general/latest/gr/rande.html#ec2_region  |
| aws_elb_name | yes | | | elb name |


Ansible modules
--------------
[ec2_elb_lb](http://docs.ansible.com/ansible/ec2_elb_lb_module.html)


Output variables
--------------
    ec2_load_balancer: created elb name 
    healthcheck_dns_name: dns name of elb

Example Playbook
----------------

   
    - hosts: localhost
      vars:
        aws_resource_tags: {
          "Name" : "my-elb-name",
          "mGage:unit" : "techops",
          "mGage:product" : "infrastructure",
          "mGage:subproduct" : "elb",
          "mGage:owner" : "sysadmin@mgage.com"
        }
        region: us-west-2
        aws_elb_instance_listeners: [{protocol: 'http',load_balancer_port: '80',instance_protocol: 'http',instance_port: '80'}]
        aws_elb_subnets: ['YOUR-SUBNET-ID-HERE,ANOTHER-SUBNET-ID-YOLO']
        aws_elb_security_group_ids: ['YOUR-SG-ID,MOAR-SG-ID']
        aws_elb_ping_protocol: "tcp"
        aws_elb_name: my-elb-name
      roles:
        - { role: ansible-role-elb }
       
        
Destroy stack
----------------


    - hosts: localhost
      vars:
        aws_elb_name: my-elb-name
      roles:
        - { role: ansible-role-elb, state: absent }
