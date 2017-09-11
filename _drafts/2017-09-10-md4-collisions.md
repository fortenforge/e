---
layout: post
title:  "Finding Collisions in MD4"
date:   2017-09-10 3:12:44 -0500
categories: crypto
use_math: true
---

On and off for the past couple of years, my friend [Ray](https://raywang.tech/) and I have been working through the [Cryptopals Crypto Challenges](https://cryptopals.com), a series of exercises split across eight sets that explore modern cryptographic protocols and their weaknesses. The challenges start out pretty easy, but as you move forward the attacks you're asked to implement become increasingly difficult.

This post is about [challenge 55](http://cryptopals.com/sets/7/challenges/55), "MD4 Collisions." In it, you're asked to implement [Wang's 2004 attack](http://www.infosec.sdu.edu.cn/uploadfile/papers/Cryptanalysis%20of%20the%20Hash%20Functions%20MD4%20and%20RIPEMD.pdf) on the MD4 hash function. The attack itself is pretty difficult: as far as Ray and I can tell, no one has posted a solution to it online. (There is [this code](https://www.bishopfox.com/resources/tools/other-free-tools/md4md5-collision-code/) from Bishop Fox, but it consists solely of a dense, 500-line, uncommented C program).



After several days of work, [our script](https://github.com/fortenforge/cryptopals/blob/master/challenges/md4_collisions.py) finally spit out a brand new collision:


![first collision]({{site.baseurl}}/assets/md4/first_collision.png)

<!-- ```
m1 = a6af943ce36f0cf4adcb12bef7f0dc1f526dd914bd3da3cafde14467ab129e640b4c41819915cb43db752155ae4b895fc71b9b0d384d06ef3118bbc643ae6384
m2 = a6af943ce36f0c74adcb122ef7f0dc1f526dd914bd3da3cafde14467ab129e640b4c41819915cb43db752155ae4b895fc71b9a0d384d06ef3118bbc643ae6384
hash = 6725aa416acc1e6adcb64c41f0f60694
``` -->

In this post we'll talk about the attack and our implementation in detail.

<!--more-->

## How MD4 works

MD4 was invented by Ron Rivest in 1990, back when no one really knew how to design cryptographically-secure hash functions. It uses a 128-bit message digest, meaning that its internal state and the hash that it outputs is 128 bits, or 16 bytes. Internally, MD4 treats this state as four 32-bit integers, which we'll call \\( a \\), \\( b \\), \\( c \\), and \\( d \\). These are initialized to four fixed constants at the start of hash function.

MD4 uses a [Merkle–Damgård construction](https://en.wikipedia.org/wiki/Merkle%E2%80%93Damg%C3%A5rd_construction), which means that the message that we want to hash is first padded to a multiple of a specific block size (16 bytes for MD4). Then, the hash function reads in the message one block at at time and uses the contents of the block to scramble its internal state. The final internal state after reading all the blocks of the message becomes the actual hash.

Several hash functions (including SHA-256) use Merkle-Damgård constructions, so the main thing that differentiates them is how they modify their state for a given message block. MD4's modification consists of 48 operations split up into 3 rounds. Each of these operations look something like this:

\\[ a = ((a + f(b, c, d) + m[i]) \mod 2^{32}) \lll s \\]

where \\(f\\) is some non-linear function that is unique to each of the three rounds and \\(m[i]\\) represents the \\(i\\)th little-endian 32-bit word of our message block. Each operation updates one of the state variables and the updates cycle between the variables in a regular order (\\(a\\) then \\(d\\) then \\(c\\) then \\(b\\)). Each round consists of 16 of these operations, each of which mix in a different \\(m[i]\\) resulting in four updates to each of the state variables per round.

You can stare at the full code below to get a better sense of how it all works:

{% highlight python %}
def _f(x, y, z): return x & y | ~x & z
def _g(x, y, z): return x & y | x & z | y & z
def _h(x, y, z): return x ^ y ^ z

def _f1(a, b, c, d, k, s, X): return _left_rotate(a + _f(b, c, d) + X[k], s)
def _f2(a, b, c, d, k, s, X): return _left_rotate(a + _g(b, c, d) + X[k] + 0x5a827999, s)
def _f3(a, b, c, d, k, s, X): return _left_rotate(a + _h(b, c, d) + X[k] + 0x6ed9eba1, s)


def md4_compress(block, state=None):

  if not state: state = [0x67452301, 0xefcdab89, 0x98badcfe, 0x10325476]
  a, b, c, d = h0, h1, h2, h3 = state

  x = list(little_endian_words(block))

  a = _f1(a,b,c,d, 0, 3, x)
  d = _f1(d,a,b,c, 1, 7, x)
  c = _f1(c,d,a,b, 2,11, x)
  b = _f1(b,c,d,a, 3,19, x)
  a = _f1(a,b,c,d, 4, 3, x)
  d = _f1(d,a,b,c, 5, 7, x)
  c = _f1(c,d,a,b, 6,11, x)
  b = _f1(b,c,d,a, 7,19, x)
  a = _f1(a,b,c,d, 8, 3, x)
  d = _f1(d,a,b,c, 9, 7, x)
  c = _f1(c,d,a,b,10,11, x)
  b = _f1(b,c,d,a,11,19, x)
  a = _f1(a,b,c,d,12, 3, x)
  d = _f1(d,a,b,c,13, 7, x)
  c = _f1(c,d,a,b,14,11, x)
  b = _f1(b,c,d,a,15,19, x)

  a = _f2(a,b,c,d, 0, 3, x)
  d = _f2(d,a,b,c, 4, 5, x)
  c = _f2(c,d,a,b, 8, 9, x)
  b = _f2(b,c,d,a,12,13, x)
  a = _f2(a,b,c,d, 1, 3, x)
  d = _f2(d,a,b,c, 5, 5, x)
  c = _f2(c,d,a,b, 9, 9, x)
  b = _f2(b,c,d,a,13,13, x)
  a = _f2(a,b,c,d, 2, 3, x)
  d = _f2(d,a,b,c, 6, 5, x)
  c = _f2(c,d,a,b,10, 9, x)
  b = _f2(b,c,d,a,14,13, x)
  a = _f2(a,b,c,d, 3, 3, x)
  d = _f2(d,a,b,c, 7, 5, x)
  c = _f2(c,d,a,b,11, 9, x)
  b = _f2(b,c,d,a,15,13, x)

  a = _f3(a,b,c,d, 0, 3, x)
  d = _f3(d,a,b,c, 8, 9, x)
  c = _f3(c,d,a,b, 4,11, x)
  b = _f3(b,c,d,a,12,15, x)
  a = _f3(a,b,c,d, 2, 3, x)
  d = _f3(d,a,b,c,10, 9, x)
  c = _f3(c,d,a,b, 6,11, x)
  b = _f3(b,c,d,a,14,15, x)
  a = _f3(a,b,c,d, 1, 3, x)
  d = _f3(d,a,b,c, 9, 9, x)
  c = _f3(c,d,a,b, 5,11, x)
  b = _f3(b,c,d,a,13,15, x)
  a = _f3(a,b,c,d, 3, 3, x)
  d = _f3(d,a,b,c,11, 9, x)
  c = _f3(c,d,a,b, 7,11, x)
  b = _f3(b,c,d,a,15,15, x)

  return [(h0 + a) & 0xffffffff, (h1 + b) & 0xffffffff, (h2 + c) & 0xffffffff, (h3 + d) & 0xffffffff]
{% endhighlight %}_

## Wang's Attack

Since MD4 is a 128-bit hash function, it should require on the order of \\( 2^{128} \\) computations to produce a preimage, and on the order of \\( 2^{64} \\) computations to produce a collision (because of the [birthday paradox](https://en.wikipedia.org/wiki/Birthday_attack)). However, soon after it was published, weaknesses were discovered which rendered attacks much more feasible. Indeed, Wang's attack claims to be able to find a collision with only \\( 2^8 \\) computations.

Wang's attack uses a technique called [differential cryptanalysis](https://en.wikipedia.org/wiki/Differential_cryptanalysis), the same method which was used to break many block ciphers in the past. (More recently, the hash function Curl which formed the basis of the [IOTA](https://iota.org/) blockchain [was broken](https://github.com/mit-dci/tangled-curl/blob/master/vuln-iota.md) using differential cryptanalysis).

Essentially, differential cryptanalysis involves carefully examining how minor differences in inputs lead to differences in outputs. What Wang found was that for a certain subset of messages that adhered to a number of constraints, a particular differential resulted in a completely identical output (i.e. a hash collision). Wang called these messages "weak."

For any weak message \\(m\\), Wang claimed that the message \\(m' \neq m\\) resulted in a collision where \\(m'\\) is defined as:

\\[ \Delta m = m' - m = (\Delta m_0, \Delta m_1, \cdots, \Delta m_{15})\\]
\\[ \Delta m_1 = 2^{31}, \Delta m_2 = 2^{31} - 2^{28}, \Delta m_{12} = - 2^{16} \\]
\\[ \Delta m_i = 0, 0 \le i \le 15, i \neq 1, 2, 12 \\]

Here, \\(m_i\\) is defined as the \\(i\\)th 32-bit little-endian word of the message \\(m\\).

So, what makes a message \\(m\\) weak? In her paper, Wang listed 121 "constraints" that she claimed were sufficient to ensure that a message was weak. ([Later research](https://eprint.iacr.org/2005/151.pdf) has shown that 2 additional constraints are actually required for sufficiency).

![Wang's 121 constraints]({{site.baseurl}}/assets/md4/constraints.png)

Each of these constraints is a condition on a single bit of a state variable at some point after one of the 48 updates. For example, the first constraint is \\(a_{1, 7} = b_{0, 7}\\). This means that after \\(a\\)'s first update, the 7th bit of \\(a\\) (1-indexing) should equal the 7th bit of \\(b\\).

The constraints are (more-or-less) independent of each other, so if you have some way of generating random messages that satisfy \\(k\\) of the 121 constraints, then on average \\(2^{121 - k}\\) of the messages you generate should satisfy the rest of the constraints (just by chance) and be weak.

It's relatively easy to satisfy the first few constraints, but as you go on it becomes harder and harder because with each new constraint you have to ensure that you don't mess up all of the prior ones. In our attack we satisfied all but 19 of Wang's constraints and 1 out of the 2 additional constraints listed in the improved attack. This suggests that we need to generate \\(2^{20}\\) or around 1 million messages to find a collision. This seems to be borne out by experimental results.

But wait: how exactly did Wang produce these 100-odd constraints? Her paper doesn't say much in this regard and other accounts online indicate that they were produced mostly by staring really hard at MD4.

<blockquote class="twitter-tweet tw-align-center" data-lang="en"><p lang="en" dir="ltr">Implementing Wang&#39;s MD4 collision attack... and all the papers I&#39;ve read say that she found the right difference patterns by &quot;intuition&quot; x.x</p>&mdash; Ray Wang (@ghostly_gray) <a href="https://twitter.com/ghostly_gray/status/906009834755055617">September 8, 2017</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

We don't actually need to know how the right constraints were arrived at to carry out our attack, but it would be nice to understand this part better. If you have a good explanation, please let me know!

## First-round constraints



## Second-round constraints
