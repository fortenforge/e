---
layout: post
title:  "DEF CON Quals 2018: official (194 pts)"
date:   2018-05-15 3:12:44 -0500
categories: ctfs pwn crypto
use_math: true
---

TL;DR: a 1 byte overflow allows you to induce a small bias in the nonce used in the DSA signing algorithm. Use LLL to exploit this bias to find the private key.

I also explore a more natural variant of the problem in which the bias is in the most significant byte of the nonce rather than the least significant, and recover the private key in this case as well.

<!--more-->

You can find the challenge files and my exploit code [here](https://github.com/fortenforge/defconquals2018-official).

## DSA

This problem is about the [Digital Signature Algorithm](https://en.wikipedia.org/wiki/Digital_Signature_Algorithm) (DSA). This blog post assumes knowledge of how the algorithm works, but if you're unfamiliar with it, you can understand it pretty easily by reading the Wikipedia page.

The most important thing to know about DSA is that its security is highly dependent on the "entropy, secrecy, and uniqueness" of \\(k\\), the nonce used in the signing step of the protocol. If the same \\(k\\) is ever used twice to sign different messages, it's a simple matter of arithmetic to derive the private key. [*fail0verflow*](https://fail0verflow.com/blog/) famously exploited exactly this vulnerability to root the PlayStation 3.

More recently, researchers discovered a flaw in the Android implementation of SecureRandom which resulted in colliding nonces in [Android Bitcoin wallets](https://www.theregister.co.uk/2013/08/12/android_bug_batters_bitcoin_wallets/). (Both of these attacks were actually on ECDSA, a variant of DSA that uses elliptic curves groups instead of multiplicative groups modulo a prime, but the vulnerability is exactly the same).

It turns out that even if there's a small bias in the values you choose for your nonce, you can still recover the private key given enough signatures on known messages. This attack is much more difficult to carry out, and is the subject of this challenge.

## Reverse Engineering

The binary is pretty simple; it lets you sign messages starting with `ls`, `du` or `stat` (but not `cat` or anything else) and execute signed messages starting with `ls`, `stat`, `du`, or `cat`. It uses the [GMP](https://gmplib.org/) library to handle the signing and verifying stages.

In order to generate \\(k\\), the nonce, it reads 20 bytes from `/dev/urandom` and then (curiously), reverses these bytes right before signing the command.

It uses `fread` to read the command to sign into a buffer of size 256. It does this one byte at a time and stops reading when it encounters a newline or when it's read 256 bytes. It replaces the newline with a null terminator, but also appends a null terminator right after the last character read if it never encountered a newline.

```c
__int64 __fastcall fread_stuff(__int64 a1, unsigned int a2)
{
  signed int i; // [rsp+18h] [rbp-8h]@1

  for ( i = 0; i < a2; ++i )
  {
    if ( (unsigned int)fread((void *)(i + a1), 1uLL, 1uLL, stdin) != 1 )
    {
      fwrite("fread fail\n", 1uLL, 0xBuLL, stderr);
      exit(1);
    }
    if ( *(_BYTE *)(i + a1) == 10 )
    {
      *(_BYTE *)(i + a1) = 0;
      return (unsigned int)i;
    }
  }
  *(_BYTE *)(i + a1) = 0;
  return (unsigned int)i;
}
```

This gives us our 1 byte overflow: if we send 256 bytes, none of which contain a newline, it will set the byte immediately after our buffer to null. As it turns out, this byte is the most-significant byte of \\(k\\), our nonce. Of course, after the reversal, this will become the least-significant byte.

## The Exploit

We have a DSA signing oracle which we can induce to sign messages with a biased nonce. The bias is small (only 8 bits), but it's enough to cause a full break. The attack is described pretty well in [this stackexchange answer](https://crypto.stackexchange.com/questions/44644/how-does-the-biased-k-attack-on-ecdsa-work).

The attack uses the [LLL algorithm](https://en.wikipedia.org/wiki/Lenstra%E2%80%93Lenstra%E2%80%93Lov%C3%A1sz_lattice_basis_reduction_algorithm) which is quite possibly the biggest cryptographic hammer out there. It can be used to break a dizzying array of cryptographic algorithms, and it shows up in CTFs [all the time](https://ctftime.org/writeups?tags=coppersmith&hidden-tags=LLL%2Ccoppersmith) these days.

The LLL algorithm solves the problem of *lattice reduction*. Simply put, given a set of linearly independent vectors \\(m_1\\), \\(m_2\\), ... \\(m_n\\), the LLL algorithm will try to find a different set of vectors \\(v_1\\), \\(v_2\\), ... \\(v_n\\) that span the same space, with the goal of making the resulting vectors *short* (small in magnitude) and *orthogonal*.

To use LLL, we have to set up our input vectors such that an (integer) linear combination of them can result in a very short vector which will indirectly yield the private key, \\(x\\). The insight is that we can express our nonce \\(k\\) as \\(256 \cdot b\\) where \\(b\\) is *small*. Specifically, \\(b\\) is about 1/256th the size of \\(q\\). With a little arithmetic we can express \\(b\\) as

\\[b \equiv u + xt \pmod{q}\\]

where \\(t\\) and \\(u\\) are simple functions of \\(r\\), \\(s\\) and our message hash \\(h\\). We can rewrite this as:

\\[b = u + xt + jq\\]

for some integer \\(j\\). The intuition here is that the right-hand side is composed of big numbers (around the same size as \\(q\\)), but the left-hand side is small. We need to translate this notion into a vectorized form, so let's say that we collect a bunch of such equations for different signature pairs \\((r_i, s_i)\\):

\\[b_1 = u_1 + xt_1 + j_1q\\]
\\[b_2 = u_2 + xt_2 + j_2q\\]
\\[\vdots\\]
\\[b_n = u_n + xt_n + j_nq\\]

Now, consider the vector \\[\mathbf{b} = \[b_1, b_2, \cdots, b_n\].\\] This vector is pretty small. Furthermore we can almost express it as a linear combination of \\[\mathbf{t} = \[t_1, t_2, \cdots, t_n\]\\] and \\[\mathbf{u} = \[u_1, u_2, \cdots, u_n\]\\] if we ignore the \\(j_iq\\) terms. If we mix in the following \\(n\\) vectors:

\\[\mathbf{q_1} = \[q, 0, \cdots 0\]\\]
\\[\mathbf{q_2} = \[0, q, \cdots 0\]\\]
\\[\vdots\\]
\\[\mathbf{q_n} = \[0, 0, \cdots q\]\\]

we can express \\(\mathbf{b}\\) as:

\\[\mathbf{b} = \mathbf{u} + x\mathbf{t} + \sum_{i = 1}^{n}j_i \mathbf{q_i}\\]

So, we have \\(n + 2\\) known vectors which can linearly combine to produce a short vector. Crucially, we don't know what linear combination will produce \\(\mathbf{b}\\), since we don't have knowledge of the private key \\(x\\), but we can use LLL to find the right weights.

When expressed as a matrix, our vectors look like:

\\[
\begin{pmatrix}
  q       &  0      &  0      & \cdots  & 0      \\\
  0       &  q      &  0      & \cdots  & 0      \\\
  \vdots  &  \vdots &  \vdots & \ddots  & \vdots \\\
  0       & 0       & 0       & \cdots  & q      \\\
  t_1     & t_2     & t_3     & \cdots  & t_n    \\\
  u_1     & u_2     & u_3     & \cdots  & u_n
\end{pmatrix}
\\]

The last trick we'll employ allows us to more easily find \\(x\\) once we've LLL-reduced our basis to produce \\(\mathbf{b}\\). Specifically, we'll augment each of our vectors with two extra dimensions:

\\[
\begin{pmatrix}
  q      & 0      & 0      & \cdots & 0      & 0   & 0   \\\
  0      & q      & 0      & \cdots & 0      & 0   & 0   \\\
  \vdots & \vdots & \vdots & \ddots & \vdots & 0   & 0   \\\
  0      & 0      & 0      & \cdots & q      & 0   & 0   \\\
  t_1    & t_2    & t_3    & \cdots & t_n    & s_T & 0   \\\
  u_1    & u_2    & u_3    & \cdots & u_n    & 0   & s_U
\end{pmatrix}
\\]

where \\(s_T = s_U = 1\\) act as *sentinel values*. When we run LLL on this basis, our short vector of interest will have \\(s_U\\) as its last element, and \\(xs_T\\) as its penultimate element. This will allow us to easily identify our short vector (as the one with 1 in its last position), and derive our private key from it (look at the second-to-last element).

This trick was originally introduced in the problem description of the corresponding [cryptopals challenge](https://toadstyle.org/cryptopals/76f2e314809b2a34ce9ff0d2a08f7a7f.txt) (the challenge is part of set 8, which you can't find on the main [cryptopals](http://cryptopals.com/sets/8) website but was released publicly anyway as a very indirect [consequence](https://twitter.com/tqbf/status/979840220660162561) of Donald Trump being elected president).

cryptopals recommends setting \\(sT\\) as \\(2^{-8}\\) and \\(sU\\) as \\(q \cdot 2^{-8}\\) in order to make each entry of the resulting short vector around the same size. In practice it doesn't matter too much, and you can get away with setting them both to 1. Just make sure to not set \\(sT\\) to something greater than \\(1\\), because this will prohibitively increase the size of our target vector since it includes an \\(xs_T\\) entry.

I implemented the [exploit](https://github.com/fortenforge/defconquals2018-official/blob/master/solve.sage) in [sage](http://www.sagemath.org/) since that's the easiest way to access an `LLL()` function from python code. The stackexchange post suggests that an 8-bit bias should require \\(n = 20\\) signature pairs, but I needed at least 66.

## What if it was the *most* significant byte?

The combination of the buffer overflow and the cryptographic attack makes this challenge quite cute. However, I'd argue the weird call to the reversal function in order to ensure that the null terminator ends up as the least significant byte of the nonce rather than the most significant byte makes it a little less elegant. Why would any real program do that?

What would happen if that reversal function was never called? Well, instead of the least significant byte being 0, the most significant byte would be 0. This is still a bias in the nonce! Since \\(q\\) is less than \\(2^{160}\\), it might not be a bias of 8 bits, but it's quite close. For our particular q the bias ends up being ~7.75 bits, which is plenty.

How would we modify our attack in this variant? Well, it's really as simple as changing all the instances of \\(2^8\\) to 1, since \\(k = b\\) is already a small number in comparison to \\(q\\).

To try this out, I created a [patched copy](https://github.com/fortenforge/defconquals2018-official/raw/master/official_msb) of the binary with the call to the nonce reversal function nopped out. I then collected the same data from this variant binary, and modified the sage exploit code to extract the private key. To try this out yourself, simply set the `msb` flag to True in the exploit code.

I'm not sure why OOO included the reversal function at all. This problem was classified under "Fruits and Desserts", so it was meant to be difficult, and it seems to me that the byte reversal was a pretty big giveaway.

## The Flag

Once we have the private key, we can simply sign a message starting with `cat`, and execute it to give us the flag.

```
(env) [defconquals2018-official]> python get_data.py interact
[+] Opening connection to 3aef2bbc.quals2018.oooverflow.io on port 31337: Done
[*] POW Challenge: 7rVwoiN0yN 22
[*] POW Solution: 890897
[*] Switching to interactive mode

------------------- OFFICIAL MENU -------------------
(S) sign
(X) execute
(E) exit
> $ X
cmd:$ cat
r:$ 175672136897532857177216578242788547073729326124
s:$ 301997289336897032672653458915890188389476020087
OK
OOO{wh0_n33d5_l34k5_wh3n_y0u_c4n_f4ul7_1nj3c7?}
```
