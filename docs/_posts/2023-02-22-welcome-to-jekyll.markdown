---
layout: post
title:  "Hidden in plain sight - encrypting messages with GPT"
date:   2023-02-22 22:08:33 +0100
categories: jekyll update
---

# Introduction

Last week someone shared the repo [Infinite-Storage-Glitch](https://github.com/DvorakDwarf/Infinite-Storage-Glitch) to hackernews. For the uninitiated, the main idea is to use the fact that Youtube allows uploads of arbitrary lenght to get free unlimited cloud storage by encoding files to black-and-white videos. There are some intricacies surrounding the YouTube compression algorithm that need to be taken care of, but apart from that it is a good proof-of-concept.

In the ensuing discussion around exotic data storage, a user going by of phh [suggested the following](https://news.ycombinator.com/item?id=34866808#34867655)

> You could use a reproducible LM (for instance using Bellard's NNCP as basis), and encode one bit in one word by taking the {first, second} most probable next word.  

Now this suggestion is reasonably infeasible, as we will see, but nevertheless 
![image tooltip here](/images/security.png){:style="display:block; margin-left:auto; margin-right:auto"}

# Theory

# The code

# Conclusions

Jekyll requires blog post files to be named according to the following format:

`YEAR-MONTH-DAY-title.MARKUP`

Where `YEAR` is a four-digit number, `MONTH` and `DAY` are both two-digit numbers, and `MARKUP` is the file extension representing the format used in the file. After that, include the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
