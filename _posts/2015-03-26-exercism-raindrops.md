---
layout: post
title: Exercism - Raindrops
date: 26 Mar 2015
---

Task: Write a program that converts a number to a string, the contents of which depends on the number's prime factors.

- If the number contains 3 as a prime factor, output 'Pling'.
- If the number contains 5 as a prime factor, output 'Plang'.
- If the number contains 7 as a prime factor, output 'Plong'.
- If the number does not contain 3, 5, or 7 as a prime factor,
  just pass the number's digits straight through.

So for example: 

{% highlight ruby %}
Raindrops.convert(1)
# => 1
Raindrops.convert(3)
# => 'Pling'
Raindrops.convert(21)
# => 'PlingPlong'
Raindrops.convert(105)
# => 'PlingPlangPlong'
{% endhighlight %}

I've seen a problem like this before. This is similar to the infamous FizzBuzz exercise, where for numbers that contain a prime factor of 3 the program outputs "Fizz", for prime factors of 5 it outputs "Buzz", and for numbers that have both 3 and 5 as prime factors it outputs "FizzBuzz."

The straightforward way of solving this is by using a `if else` block and some logical operators:
{% highlight ruby %}
if num % 5 == 0 && num % 3 == 0
    "FizzBuzz"
elsif num % 3 == 0
    "Fizz"
elsif num % 5 == 0
    "Buzz"
else
    num
end
{% endhighlight %}

It's the straightforward answer, without any needlessly complex methods or algorithms. Wrap this in a method that takes in `num` as a parameter and you're set.

However, this code is specific. It only works if you want prime factors of 3 to output "Fizz" and prime factors of 5 to output "Buzz." I want to write this in such a way that even if the specifications change I only need to make minimal changes to the code.

My first thought was to use a Hash object to contain the prime factors and the output.

{% highlight ruby %}
SOUNDS = { 3 => "Pling", 5 => "Plang", 7 => "Plong"}
{% endhighlight %}

And from there the method iterates through the `SOUNDS` hash and adds the value of the key value pair if the result of `number % key` is equal to zero.

The final code looks like this:

{% highlight ruby %}
class Raindrops
  SOUNDS = { 3 => "Pling", 5 => "Plang", 7 => "Plong"}

  def self.convert(number)
    final_sound = ""
    SOUNDS.each do |key, sound|
      final_sound << sound if (number % key) == 0
    end
    if final_sound == ""
      final_sound << number.to_s
    end
    final_sound
  end
end
{% endhighlight %}

This code is easier to change. If I wanted prime factors of 13 to output "Plung" it's as simple as adding another key value pair to `SOUNDS` instead of changing the logic in the `convert` method.

[The Github repository can be found here](https://github.com/crackedlcd/exercism-raindrops)