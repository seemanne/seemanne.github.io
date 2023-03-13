---
layout: post
title:  "Hidden in plain sight - encrypting messages with GPT"
date:   2023-02-22 22:08:33 +0100
categories: jekyll update
---

# Introduction

Some weeks ago someone shared the repo [Infinite-Storage-Glitch](https://github.com/DvorakDwarf/Infinite-Storage-Glitch) to hackernews. For the uninitiated, the main idea is to use the fact that Youtube allows uploads of arbitrary lenght to get free unlimited cloud storage by encoding files to black-and-white videos. There are some intricacies surrounding the YouTube compression algorithm that need to be taken care of, but apart from that it is a good proof-of-concept of creative storage.

In the ensuing discussion around exotic data storage, a user going by of phh [suggested the following](https://news.ycombinator.com/item?id=34866808#34867655)

> You could use a reproducible LM (for instance using Bellard's NNCP as basis), and encode one bit in one word by taking the {first, second} most probable next word.  

Now this suggestion is reasonably infeasible, as we will see, but it contains the right amount deranged crazy to make for a good side project. Before we get to the proof-of-concept code however, lets recap some basics on language models to understand the theory behind phh's suggestion.

# Lifting the veil of NLP for an inch

## Toy Model
In some circles, generative large language models (LLM) are often described as "fancy autocomplete machines". While this is by now a pretty bad first order approximation of these models, it serves as a good mental model to desribe phh's suggestion in laymans terms. If we imagine a LLM like the autocomplete suggestions on a smartphone keyboard, the idea is essentially to encode data bit-by-bit by choosing between the middle suggestion (0) and the left suggestion (1) depending on the value of the bit you're trying to encode.  
So lets try to work this out by encoding the sequence 0010101: We first inspire our autocomplete keyboard by writing the promt `My name is Teven and I am `. On my iPad this autocompletes to `My name is Teven and I am so happy to be able with you`. To decode the same sentence we again write out our promt and and now write down the "rank" of the word in our sentence. 
![not exactly what we want](/images/ipad_autocomplete.jpeg){:style="display:block; margin-left:auto; margin-right:auto"}  
As we can see in the screenshot, the apple autocomplete is subpar for this purpose. During the encoding step, the word `so` was at the middle position while during our decoding step it now sits at the undefined rightward position for the same prompt. This defines our first constraint (A) for the encoding to work: The encoding must be 100% reproducible. The encoded sentence provided by the autocomplete also motivates a second constraint (B) we would ideally like to fulfill: The encoded data should make sense to some degree. These two constraints are at leastsomewhat at odds with each other. This is due to the way that most language models are built.

## Generative LLM

Most natural language models are based on a fairly simple pipeline. First we have an encoder (referred to as a tokenizer) which translates language, which lives in the world of letters, words and sentences, into vectors in some higher-dimensional space. As there is a computational cost associated to the dimension of these vectors we cannot simply assign a dimension in the space to every possible english word. Instead we use building blocks of words, called tokens, to manage the dimensionality of the space. Tokenization is a science by its own right, for people who would like to see a quick and dirty overview of different methods and various problems not mentioned here, I recommend the excellent [huggingface documentation article](https://huggingface.co/docs/transformers/main/en/tokenizer_summary).  

After transforming our sentences into vectors they are put into the core of the model, the neural network. For most language models, this neural net is a variant of a transformer, an architecture [open sourced by Google in 2017](https://arxiv.org/abs/1706.03762). As a sidenote, the transformer is what puts the T in GPT, for those still unaware. The output of this neural net is something akin to a probability distribution over the next token. This probability distribution is the output we want to use for our purpose. Aside from [dropout](https://arxiv.org/abs/1207.0580), which is usually deactivated during inference, this neural net is strictly deterministic. So if we feed it the same tokens as input, it will produce the same output. This means if we just switch between the most-likely and the second-most-liekly token depending on the bit we want to encode and then feed those tokens to the tokenizer we can get a nice encoding of our bitsting into text. So lets create a simple proof-of-concept based on this idea. For the model we will use GPT-2 which is freely accessible on huggingface. We also choose to "seed" the model with a predefined promt to start generating from.

{% highlight python %}
import torch
from transformers import GPT2Tokenizer, GPT2LMHeadModel

def encode_to_text(promt, binary):

    tokenizer = GPT2Tokenizer.from_pretrained('gpt2')
    model = GPT2LMHeadModel.from_pretrained('gpt2')

    for i in binary:

        encoded_input = tokenizer(promt, return_tensors='pt')
        output = model(**encoded_input)
        next_token_logits = output.logits[0, -1, :]
        possible_tokens = torch.argsort(next_token_logits, dim=-1, descending=True)
        next_token = possible_tokens[i]
        promt = promt + tokenizer.decode(next_token)

    return promt
{% endhighlight %}

The code is very short so lets go through it step-by-step.  
First we set up the tokenizer and the model using transformers. Transformers provides a wrapper for the underlying models. This allows us to access both high-level functions, such as the `model.generate()` function but also play with the raw torch model directly.

{% highlight python %}
    tokenizer = GPT2Tokenizer.from_pretrained('gpt2')
    model = GPT2LMHeadModel.from_pretrained('gpt2')
{% endhighlight %}

Next we encode the promt using the tokenizer. 




{% highlight python %}
yeet=yeet
{% endhighlight %}


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

![image tooltip here](/images/security.png){:style="display:block; margin-left:auto; margin-right:auto"}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
