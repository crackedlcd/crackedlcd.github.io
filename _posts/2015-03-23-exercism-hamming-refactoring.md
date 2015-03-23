---
layout: post
title: Exercism - Hamming Part 2 - Refactoring
date: 23 Mar 2015
---

[My first iteration of calculating the Hamming distance](/2015/03/19/Exercism-Hamming/) worked well enough, but after some looking around I realized that my code could be refactored down thanks to some built-in Ruby functions.

{% highlight ruby linenos %}
class Hamming
  def self.compute(base, compare)
    raise ArgumentError if base.length != compare.length
    diff = 0
    (0..(base.length - 1)).each do |index|
      if base[index] != compare[index]
        diff += 1
      end
    end
    diff
  end
end
{% endhighlight %}

The code is pretty straightforward. In a loop I compare  ``base`` and ``compare`` by each character and add one to ``diff`` whenever it records a difference, then return ``diff``.

There's a Ruby method that can do this all in one line, the ``reduce`` method. According to the Ruby Docs, the reduce method:

"Combines all elements of enum by applying a binary operation, specified by a block or a symbol that names a method or operator.

"If you specify a block, then for each element in enum the block is passed an accumulator value (memo) and the element. If you specify a symbol instead, then each element in the collection will be passed to the named method of memo. In either case, the result becomes the new value for memo. At the end of the iteration, the final value of memo is the return value for the method."

For example, if I want to multiply every number in an array of numbers called ``arr``:

{% highlight ruby %}
arr.reduce(:*)
{% endhighlight %}

Now for the actual code. ``reduce`` is going to be called from the ``(0..base.length)`` Range object. From there a block is called that takes two arguments, ``diff`` and ``index``. In the block there's a terney operator that sees if ``base[index]`` is different from ``compare[index]`` and if there is add one to ``diff``, otherwise keep ``diff`` at the same value. I can keep it as ``base.length`` instead of ``base.length-1`` since any index outside the array is ``nil``, so only the false case would be ran. 

The code ends up looking like this:

{% highlight ruby linenos %}
class Hamming
  def self.compute(base, compare)
    raise ArgumentError if base.length != compare.length
    (0..(base.length)).reduce(0) { |diff, index| base[index] != compare[index] ? diff + 1: diff }
  end
end
{% endhighlight %}
[You can see the final code at my Github page here.](https://github.com/crackedlcd/exercism-hamming)