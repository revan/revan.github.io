---
layout: post
title: "Stripping HTML with Regex"
---

I wanted to strip every set of angle brackets in a file, and googling just reminded me that [you can't parse HTML with regex](http://stackoverflow.com/a/1732454).

The vim command for this would be
{% highlight bash %}
:%s/\(<[^>]*>\)\+/\r/g
{% endhighlight %}

which replaces any amount of angle brackets with a carriage return.
