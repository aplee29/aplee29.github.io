---
layout: post
title:  "What was interesting about Angular Filters?"
date:   2016-08-09 00:12:43 -0400
---

Filters are functions that can be implemented in AngularJS to manipulate, or *filter* data. One aspect that I found to be particularly useful is the way in which filters can be used to easily display data to users of a web application.

These are a few of the built-in filters provided by AngularJS:

* `currency` : number -> currency
* `date` : date -> string based on desired format
* `number` : number -> string
* `lowercase` : string -> lowercase
* `uppercase` : string -> uppercase

In the view, a filter can be applied to an expression by using the pipe symbol `|`. 

The generic syntax for a filter is: {% raw %}`{{ expression | filter }}`{% endraw %}.

Filters can also have its own filter criteria, or argument. This can be represented as: {% raw %}`{{ expression | filter:argument }}`{% endraw %}. 

Let's consider the following:

1. {% raw %}`{{ 100 | currency }}`{% endraw %}
2. {% raw %}`{{ 98765 | number:2 }}`{% endraw %}

In the first example, we utilize the `currency` filter to format the *number* **100** into its *currency* form. This returns the value **$100**. (Currencies other than the USD can also be specified). The second example applies the `number` filter to **98765**. However, this time, our filter also has an argument, **2**. This instructs the filter to round the number to 2 decimal places, which produces the value **98,765.00**.

The `date` filter in particular is very versatile in that it offers multiple ways of formatting the date. Here are a few examples, as well as their respective values:

1. {% raw %}`{{xxxxxxxxxxxxx | date:'medium'}}`{% endraw %} => Aug 8, 2016 11:34:18 PM
2. {% raw %}`{{xxxxxxxxxxxxx | date:'MM/dd/yyyy @ h:mma'}}`{% endraw %} => 08/08/2016 @ 11:34PM
3. {% raw %}`{{xxxxxxxxxxxxx | date:'yyyy-MM-dd HH:mm:ss'}}`{% endraw %} => 2016-08-08 23:34:18

*Note: The `xxxxxxxxxxxxx` represents the Unix timestamp of the current date and time.*

Although this post covers just one aspect of Angular filters, there are other notable advantages to using this function, such as searching for an individual user from a list. This may come especially in handy when we are dealing with large sets of data. 
