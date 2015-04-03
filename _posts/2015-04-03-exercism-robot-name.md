---
layout: post
title: Exercism - Robot Name
date: 03 Apr 2015
---

Task: Write a program that manages robot factory settings.

When robots come off the factory floor, they have no name.

The first time you boot them up, a random name is generated, such as
RX837 or BC811.

This is a straightforward exercise. Basically all I need to do is create a new randomized string on ``Robot`` initialization and on any ``reset`` call. So far I know that a ``Robot`` class:

1. Generates a ``name`` on initialization.
2. Has a ``reset`` method that creates a new randomized ``name``.

Since the ``Robot`` class initialization and the ``reset`` method performs the same task my first thought was to create a ``generate_name`` method that creates a new Robot name.

After the first pass through my code looks like this: 

{% highlight ruby linenos %}
class Robot
  attr_reader :name

  def initialize
    @name = generate_name
  end

  def reset
    @name = generate_name
  end

  private

  def generate_name
    value = ""
    letters = ('A'..'Z').to_a
    2.times { value << letters[rand(26)] }
    3.times { value << rand(10).to_s }
    value
  end
end
{% endhighlight %}

This code is a start, but it can be improved upon. The biggest flaw with this code is that *it doesn't guarantee unique robot IDs.* It's just highly likely two randomly generated Robot IDs are unique considering there are a total of 676,000 possible combinations. But I don't want *highly likely*, I wan't *guaranteed.*

Thankfully there's already a Ruby library that can help out with this, the Set library. Instead of passing every Robot ID into an array and checking for any equal entries, I just call the ``add`` method on the Set and it takes care of checking for same entires for me. If there is already an entry present the ``add`` method just returns `nil`, which is helpful.

The one issue I initially had is how to keep track of all the names amongst every instance of the `Robot` class. After some Google and Stack Overflow searching I found out about class variables. To create one you just call it as ``@@class_variable_name`` which makes it convinent to store values shared amongst the class.

Also, instead of calling `2.times`  and `3.times` it's more idiomatic to use the ``sample`` method. So, outputting the randomized ID will now look like this:

{% highlight ruby %}
(('A'..'Z').to_a.sample(2) + (0..9).to_a.sample(3)).join
{% endhighlight %}

The final code ends up being: 

{% highlight ruby linenos %}
require 'set'

class Robot
  attr_reader :name
  @@name_set = Set.new

  def initialize
    @name = generate_name
  end

  def reset
    @@name_set.delete(@name)
    @name = generate_name
  end

  private

  def generate_name
    value = ( ('A'..'Z').to_a.sample(2) + (0..9).to_a.sample(3) ).join
    if @@name_set.add?(value)
      value
    else
      generate_name
    end
  end
end
{% endhighlight%}

The use of recursion in the `generate_name` method is to me the most straightfoward way of regenerating names in case ``@@name_set.add?`` returls ``nil``. If the name is already taken, run it again. It's unlikely that there would be many collisions in the Set so there shouldn't be any stack overflow issues that might occur with recursive calls.

[You can see the code on my Github page here](https://github.com/crackedlcd/exercism-robotname)