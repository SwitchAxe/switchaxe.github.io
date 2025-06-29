---
layout: post
title: "Why Ruby hashmaps are amazing"
date: 2025-06-29
categories: Programming Ruby
---

# What is a hashmap?
A hashmap, sometimes called dictionary, association list, hash table or at times just map, is, put simply, a data structure comprised of key-value pairs where every key is associated with its own value.  
In Ruby they work like this:  
{% highlight ruby %}
# Note that many different types of values appear both as keys and as values. In fact, most data types are accepted in a hashmap in this way!
my_hashmap = { 0 => 1, "foo" => "bar", :abc => :def }
{% endhighlight %}  

# So what's the big deal?
At this point, you'll probably be wondering how this is any different from the hashmaps you may have found in other languages like Cpp, Java, Python, or even more exotic ones like Scheme...  
Ruby hashmaps have a certain feature called the `default` element.  
The `default` element returns a set value whenever we try to access a key that is not otherwise present in the map.  
By default this value is `nil`, so unsurprisingly  
{% highlight ruby %}
my_hashmap[42]
{% endhighlight %}  
returns `nil`. The `default` element of any Ruby hashmap is stored in the `default` attribute, freely accessible and, most importantly, overwriteable:
{% highlight ruby %}
my_hashmap.default = "<3"
{% endhighlight %}  
Predictably, `my_hashmap[42]` will now return the string "<3", as will any other value not already stored in the map.  

Hashmaps also have a `default_proc` attribute that uses a **proc**edure (a.k.a. a function), to compute the returned value.  
default procedures are assigned like this:
{% highlight ruby %}
# default procs take two arguments as input: the hash table itself and the key, in order

# defining a default proc with the standard function definition syntax
def my_procedure(hash, key)
  # the #{} inside the string is an interpolation to add 'key' as the value of the argument itself rather than a string
  "#{key} not found!"
end
my_hashmap.default_proc = my_procedure

# but Ruby has several different syntax constructs for as many types of function. Here are a few more ways to do the same thing:
my_hashmap.default_proc = -> (h, k) { "#{k} not found!" }
my_hashmap.default_proc = proc { |h, k| "#{k} not found!" }
{% endhighlight %}  
This way, `my_hashmap[42]` will return `"42 not found!"` and thus we have solved the issue of being limited to an hard-coded value for keys that are not in our map.  
However, don't be fooled: the values our hashmap **contains** haven't actually changed. Rather, we're computing the string every time with the default procedure since our hashmap hasn't actually _stored_ anything.  
Indeed, the size of our map hasn't changed:  
![image](/assets/images/2025-06-29-RUBY-HASHMAPS-1/1.png)  
(screenshot of a code snippet running in the `irb` interactive Ruby interpreter)  
Since the default procedure is just that, a procedure, we can do any computation we want in it, including assigning hashmap values:

{% highlight ruby %}
my_hashmap.default_proc = -> (h, k) { h[k] = "#{k} found!" }
my_hashmap[42] # returns "42 found!" and stores it, increasing the size from 3 to 4
my_hashmap["hello world"] # returns "hello world found!" and stores it, increasing the hashmap size to 5
{% endhighlight %}  
The code above works because in Ruby, just like most languages you're probably used to, assignments return the new value of the assigned variable: `var = 5` is a valid expression with the value 5 (that is, the value of `var` after being assigned)
