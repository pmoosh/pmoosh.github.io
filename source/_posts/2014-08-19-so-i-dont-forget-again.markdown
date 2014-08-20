---
layout: post
title: "So I don't forget again"
date: 2014-08-19 23:30:56 -0700
comments: true
categories: [Chef, Knife, Data Bags] 
---

This is one of the blog posts that is mostly for I am still digging into chef and all its details. There are many blogs out there, however chef develops so rapidly that many of the blogs are outdated. I am sure this will also happen to this blog entry....

Today I had to work with encrypted data bags. They are easy to create with knife and a chef server:
```
knife data bag from file DATA_BAG_NAME /path/to/DATA_BAG_ITEM.json -z --secret-file /path/to/encrypted_data_bag_secret
```

But since I needed them locally on my system - I am using chef solo with vagrant - I wanted to download them again. So here is how I got it done. There might other better ways....
```
knife data bag show DATA_BAG_NAME --format json DATA_BAG_ITEM > /path/to_encrypted/DATA_BAG_ITEM.json
```
