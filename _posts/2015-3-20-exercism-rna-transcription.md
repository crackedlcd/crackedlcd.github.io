---
layout: post
title: Exercism - RNA Transcription
date: 20 Mar 2015
---

Task: Write a program that, given a DNA strand, returns its RNA complement (per RNA transcription).

There needs to be a ``Complement`` class and it needs to have two methods, a ``of_dna`` method and a ``of_rna`` method, and both of these methods return the DNA complement. For example:

{% highlight ruby %}
Complement.of_dna 'A'
# => 'U'

Complement.of_rna 'U'
# => 'A'
{% endhighlight%}

The class so far looks like this:

{% highlight ruby %}
class Complement
    def self.of_dna(nucleotide)
    end

    def self.of_rna(nucleotide)
    end
end
{% endhighlight %}

My first thought for returning the complement was to iterate through the nucleotide, run a function to get the complement base pair, push it into a complement string, and return the string. The function that returns the complement base pair should take in a nucleotide and return a nuceltide. A case statement seemed like a good idea at the time.

{% highlight ruby %}
case nucleotide
when 'A'
    'U'
when 'G'
    'C'
    # repeat for all nucleotides
end
{% endhighlight %}

So the final code ended up looking something like this:

{% highlight ruby linenos %}
class Complement

  def self.of_dna(nucleotide)
    final = ""
    nucleotide.each_char do |n|
      raise ArgumentError if n == 'U'
      final << switch_nucleotide(n, true)
    end
    final
  end

  def self.of_rna(nucleotide)
    final = ""
    nucleotide.each_char do |n|
      raise ArgumentError if n == 'T'
      final << switch_nucleotide(n, false)
    end
    final
  end

private
  def self.switch_nucleotide(character, rna) 
    case character
    when "G"
      "C"
    when "C"
      "G"
    when "A"
      if rna 
        "U"
      else
        "T"
      end
    when "T"
      "A"
    when "U"
      "A"
    end
  end

end
{% endhighlight %}

*Yikes!* It passes all the tests given by Exercism but it's a gigantic mess, especially the ``switch_nucleotide`` function. In the next part, I tackle refactoring.