---
layout: post
title: Exercism - Hamming
date: 19 Mar 2015
---

Task: Write a program that can calculate the Hamming difference between two DNA strands.

The Hamming distance between two strands of DNA by counting the number of differences between the two. So, for example:
{% highlight text %}
GGACG
GGTCG
{% endhighlight %}

The Hamming distance between these two DNA strands is 1.

The implementation of the code should look like this:

{% highlight ruby %}
Hamming.compute('GGACG', 'GGTCG')
# => 1
{% endhighlight %}

Now for the code writing part. So far I know there needs to be a ``Hamming`` class and that has a function ``compute`` and it expects two strings of the same size. 

{% highlight ruby %}
class Hamming
    def self.compute(base, compare)
    end
end
{% endhighlight %}

The first solution that came to mind was to iterate through each character in the ``base`` string and compare the value between the ``base`` string and ``compare`` string. Since both strings are of the same size we only need to iterate through once string. So the algorithm will be of linear time complexity.

Since the index position of base goes from 0 to ``base.length - 1``, it made the most sense to use a Range object. The Range object will look something like ``(0..(base.length - 1))``. From there I just called the ``.each`` method to access the inidivdual characters in the string, adding one to a diff variable whenever the ``base[index]`` does not match ``compare[index]``

The first iteration of the code looks like this:

{% highlight ruby %}
class Hamming
  def self.compute(base, compare)
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

This passes all the test cases supplied by Exercism.io. However, this will still run even if the two arguments are not of the same length. Since Hamming distance is only defined for sequences of equal length I'll want to guarantee that, so I need to raise an exception when ``base.length`` and ``compare.length`` are unequal.

{% highlight ruby %}
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

Since none of the pre-made test cases test for this, I added my own.

{% highlight ruby %}
def test_exception_raised_if_different_lengths 
    assert_raises ArgumentError do 
        Hamming.compare('A', 'AA')
    end
end

{% endhighlight %}

And it passes. [You can see the final code at my Github page here.](https://github.com/crackedlcd/exercism-hamming)