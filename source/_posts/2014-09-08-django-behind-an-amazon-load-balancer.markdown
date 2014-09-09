---
layout: post
title: "Django behind an Amazon Load Balancer"
date: 2014-09-08 16:58:20 -0700
comments: true
categories:[AWS, django] 
---

We had to configure django behind a AWS load balancer and that would mean that whenever the IP of the load balancer changes we would to change the ALLOWED_HOSTS
in the settings section. Note: This is for the ELB health check. Some on the web suggest to circumvent the ALLOWED_HOSTS by simple allowing any host, 
which we didn't want to do. So here is the solution
I found on [Daniel Ryan's](http://dryan.me/articles/elb-django-allowed-hosts/) site:

```
# Hosts/domain names that are valid for this site; required if DEBUG is False
# See https://docs.djangoproject.com/en/1.5/ref/settings/#allowed-hosts
ALLOWED_HOSTS = [
    'WHATEVER YOU NEED'
]
 
import requests
EC2_PRIVATE_IP  =   None
try:
    EC2_PRIVATE_IP  =   requests.get('http://169.254.169.254/latest/meta-data/local-ipv4', timeout = 0.01).text
except requests.exceptions.RequestException:
    pass
 
if EC2_PRIVATE_IP:
    ALLOWED_HOSTS.append(EC2_PRIVATE_IP)
```
