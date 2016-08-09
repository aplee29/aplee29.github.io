---
layout: post
title:  "What was interesting about Angular Filters?"
date:   2016-08-09 00:12:43 -0400
---

Filters are functions that can be implemented in AngularJS to manipulate, or *filter* data. One aspect that I found to be particularly useful was the way in which filters can be used to easily display data to users of a web application.

These are a few of the built-in filters provided by AngularJS:

* **currency** : number -> currency
* **date** : date -> string based on desired format
* **number** : number -> string
* **lowercase** : string -> lowercase
* **uppercase** : string -> uppercase

In the view, a filter can be applied to an expression using a pipe symbol, |. 

The generic syntax for a filter is: `{{ expression | filter }}`.


