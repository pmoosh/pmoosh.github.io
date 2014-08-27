---
layout: post
title: "Mac OSX - pbcopy and pbpaste"
date: 2014-08-18 18:06:45 -0700
comments: true
categories: [Mac OSX, Command Line]
---


One of the small but helpful tools that Mac OS X provides are pbcopy and pbpaste.
They provide copying and pasting to the pasteboard (the Clipboard) from the command line.

So you can pipe your massive json file onto the pastboard with a simple command:

```
cat huge.json | pbcopy
```

Now you have it in the pasteboard (clipboard) and paste it in your favorite json lint site - like [JsonLint](http://jsonlint.com) or [FREEFORMATTER.COM](http://www.freeformatter.com/json-validator.html). Another site I was just recently introduced to is the [Collapsible JSON Formatter](http://www.bodurov.com/JsonFormatter/).

One way I use pbpaste is when copying keys created on websites. After I copied the key to the pasteboard I will run the command:
```
pbpaste > keyfile.pem
```

That's it.
