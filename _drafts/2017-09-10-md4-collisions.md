---
layout: post
title:  "Finding Collisions in MD4"
date:   2017-09-10 3:12:44 -0500
categories: crypto
use_math: true
---

On and off for the past couple of years, my friend [Ray](https://raywang.tech/) and I have been working through the [Cryptopals Crypto Challenges](https://cryptopals.com), a series of exercises split across eight sets that explore modern cryptographic protocols and their weaknesses. The challenges start out pretty easy, but as you go on the attacks you're asked to implement become increasingly difficult.

This post is about [challenge 55](http://cryptopals.com/sets/7/challenges/55), "MD4 Collisions." In it, you're asked to implement [Wang's 2004 attack](http://www.infosec.sdu.edu.cn/uploadfile/papers/Cryptanalysis%20of%20the%20Hash%20Functions%20MD4%20and%20RIPEMD.pdf) on the MD4 hash function. The attack itself is pretty difficult; as far as Ray and I can tell, no one has posted a solution to it online. (There is [this code](https://www.bishopfox.com/resources/tools/other-free-tools/md4md5-collision-code/) from Bishop Fox, but it consists solely of a dense, 500-line, uncommented C program).



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

Several hash functions (including SHA-256) use Merkle-Damgård constructions, so the main thing that differentiates them is how they modify their state given a message block. MD4's modification consists of 48 operations split up into 3 rounds. 

## Wang's Attack

Since MD4 is a 128-bit hash function, it should require on the order of \\( 2^{128} \\) computations to produce a preimage, and on the order of \\( 2^{64} \\) computations to produce a collision (because of the [birthday paradox](https://en.wikipedia.org/wiki/Birthday_attack)). However, soon after it was published weaknesses were discovered which rendered attacks much more feasible. Indeed, Wang's attack claims to be able to find a collision with only \\( 2^8 \\) computations.
