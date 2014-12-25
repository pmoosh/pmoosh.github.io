---
layout: post
title: "Basics Operation on a private docker registry"
date: 2014-12-24 23:51:58 -0800
comments: true
categories: Docker
---
Basic Operations on a private Docker registry
---------------------------------------------

Getting started with a private Docker registry means a lot of searching on Google. Unfortunately, I couldn't find a single site that would cover the basic operations such as search for images or tags. So here is a little write up, that I covers some of it.

### Docker Login

In order to do push and pull operations on the private registry a user has to be authenticated (via nginx proxy) and logged in.

```
docker login docker.example.com
```
This will generate a `.dockercfg` file on the system where the login will be stored.

### Search the private index

There are two methods for search available:

#### Docker Search

Docker search allows for some simple searches in the private index. For example:

```
docker search docker.example.com/ubuntu
```
However, if a nginx proxy sits in front of private Registry for authentication, you need to add user and password in front of the URL (Note: Unfortunately `docker search` will not take the login credentials as stored in the `.dockercfg` file):
```
docker search user:password@docker.example.com/ubuntu

NAME             DESCRIPTION   STARS     OFFICIAL   AUTOMATED
library/ubuntu                 0
```
This will give you some information about the ubuntu repositories stored in the private registry, if one exists. `docker search` also allows for partial searches, so such as using `ubu` instead of `ubuntu` as search terms.

#### [Docker Registry API](https://docs.docker.com/reference/api/registry_api/)

A less elegant but much more flexible way to search the registry is the use of the Docker Registry API

```
curl -X GET https://user:passwd@docker.example.com/v1/search?q=ubuntu
```

or a more generic search:

```
curl -s -X GET https://user:passwd@ocker.example.com/v1/search
```
generates json lists, which can be further formated with a tool like [jq](http://stedolan.github.io/jq/)
For example the following command line:
```
curl -s -X GET https://user:passwd@docker.example.com/v1/search | jq '.results[].name'
```
would produce an output like this:

```
"library/nginx"
"library/aws_cli"
"library/redis"
"library/debian"
"library/busybox"
"library/ubuntu"
"library/commit_test"
"library/logstash"
"library/logstash-forwarder"
"library/base"
```

### Delete Operations

Delete operations are only available via the Docker Registry API. Note there is easy way to remove a image from a private registry, something that could help to recude storage space. The reason for is that layers of one image may rely layers from other images.

#### Delete a repository tag

```
curl -X DELETE https://user:passwd@docker.example.com/v1/repositories/library/ubuntu/12.04
```

#### Delete a complete repository

(using the ubuntu repo as example)
```
curl -X DELETE https://user:passwd@docker.example.com/v1/repositories/library/ubuntu/
```

### Checking repository tags

Here is an example (using the ubuntu repo) of checking the exixting tags in a repository:
```
curl -X GET https://user:passwd@docker.example.com/v1/repositories/library/ubuntu/tags
```
Example output:
```
{"12.04": "58c0a77963eaca09a68febbda32683812f3ff3615cc1ffa305cee67aa591fe26",
"14.04": "86ce37374f40e95cfe8af7327c34ea9919ef216ea965377565fcfad3c378a2c3"}
```
### Pushing Container images to the private Registry

#### Tagging a Container Image

In order to push a container image to a private Docker Registry the image needs to tagged accordingly. This can be done when the container is built.

```
docker build --rm -t docker.example.com/logstash-fwd .
```
This will automatically tag the images with the `latest` tag. To mark the images with a more specific version tag run the following command:
```
docker tag docker.example.com/logstash-fwd docker.example.com/logstash-fwd:0.0.1
```
After these two operations the image will have two tags `0.0.1` and `latest`. The `latest` tag is implicit if you pull the image with the registry name only.

#### Pushing the Container Image to the Registry

After the image is tagged correctly it can be pushed to the registry with command:
```
docker push docker.example.com/logstash-fwd:0.0.1 .
```
Note: For this operation you have to be [logged in](#Docker Login) into the registry. The docker-client will not throw an error if you are not.
