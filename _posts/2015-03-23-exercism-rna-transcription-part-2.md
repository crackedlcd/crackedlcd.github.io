---
layout: post
title: Exercism - RNA Transcription Part 2
date: 23 Mar 2015
---

In the [previous post](/2015/03/20/exercism-rna-transcription/) I finished the RNA Transcrpition Exercism challenge with some, in my opinion, *terrible* looking code. It works but I felt it needing some heavy refactoring. Let's see what can be done.

The first bit of code on the chopping block was the ``switch_nucleotide`` function. When you look at the code:

{% highlight ruby %}
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
{% endhighlight %}

The function takes in the DNA or RNA base as a character and you return the complement. Turns out you don't even need a method to be able to do that. It would be a lot simplier to write this out as a Hash.

{% highlight ruby %}
DNA_TO_RNA = { "G" => "C", "C" => "G", "T" => "A", "A" => "U"}
RNA_TO_DNA = DNA_TO_RNA.invert
{% endhighlight %}

``RNA_TO_DNA`` is just the invert of ``DNA_TO_RNA`` because *that's what it is.* While it's unlikely that DNA nucleotides are going to change any time soon, this code is easier to change in the future.

Now I needed to change both the ``of_dna`` and ``of_rna`` methods to reflect this.

{% highlight ruby %}
def self.of_dna(nucleotide)
  final = ""
  nucleotide.each_char do |n|
    raise ArgumentError if n == 'U'
    final << DNA_TO_RNA[n]
  end
  final
end

def self.of_rna(nucleotide)
  final = ""
  nucleotide.each_char do |n|
    raise ArgumentError if n == 'T'
    final << RNA_TO_DNA[n]
  end
  final
end
{% endhighlight %}

There's still repeated code that can be DRYed up. The only difference between the two is the conversion hash used, so the new method needs to have that passed in as an argument. Also, the ``ArgumentError`` raised is specific only to 'U' and 'T', so if for some reason someone decided to throw in a 'Z' in the nucleotide string instead of throwing an error the method continutes as if nothing went wrong. 

This can be fixed by changing the ArgumentError logic to raise unless the nucleotide exists as a key, which in Ruby code is written as:``raise ArgumentError unless conversion_type.keys.include? n``

My final code looks like this: 

{% highlight ruby linenos %}
class Complement
  DNA_TO_RNA = { "G" => "C", "C" => "G", "T" => "A", "A" => "U"}
  RNA_TO_DNA = DNA_TO_RNA.invert

  def self.of_dna(nucleotide)
    set_complement DNA_TO_RNA, nucleotide
  end

  def self.of_rna(nucleotide)
    set_complement RNA_TO_DNA, nucleotide
  end

private
  def self.set_complement(conversion_type, nucleotide)
    final = ""
    nucleotide.each_char do |n|
      raise ArgumentError unless conversion_type.keys.include? n
      final << conversion_type[n]
    end
    final
  end
end
{% endhighlight %}

Looks a better, doesn't it?