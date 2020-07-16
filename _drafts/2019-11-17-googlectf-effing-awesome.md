---
layout: post
title:  "Google CTF 2018: effing awesome"
date:   2019-11-17 3:12:44 -0500
categories: ctfs
---

TL;DR: escape a python3 jail using only special characters and the letter 'f'. This was a fun challenge that I testsolved with [qzqxq](https://galhacktictrendsetters.wordpress.com/author/qzqxq/) for the 2019 Google CTF Finals.

You can find the challenge files and my exploit code [here](https://github.com/fortenforge/googlectf-effing-awesome).

<!--more-->
## The Jail

You can find the source code for the challenge [here](https://github.com/fortenforge/googlectf-effing-awesome/blob/master/service.py). It looks complicated but it's actually fairly straightforward: just fire up a python3 CLI and enter the following six lines:

```python
forbidden  = [input, print, chr, ord, type, locals, compile, repr, globals]
forbidden += [setattr, memoryview, exec, __import__, eval ]
for f in forbidden:
  delattr(__builtins__, f.__name__)

del forbidden
```

Did that? OK. Your goal now is to execute a shell command (like say, `cat`ing a file) only typing the following characters:

```
 !"#$%&'()*+,-./:;<=>?@f[\\]^_`{|}~\n\r
```

## Regaining Numbers

More so than most other types of CTF challenges, jail escapes tend to rely quite heavily on tricks, and reading a solution often doesn't give you insight into how the author came up with their exploit themselves. Given this, I think it will be helpful to walk through our thought process in detail for this challenge.

The first thing I thought of was [this writeup](http://wapiflapi.github.io/2013/04/22/plaidctf-pyjail-story-of-pythons-escape) from the 2015 Plaid CTF. The jail here was if anything far more restrictive: they couldn't use use the letter 'f' for one thing, and the jail wouldn't simply run whatever command you entered.

Their exploit starts with a classic trick you can use to produce integers without access to decimal characters. The string `[] <= []` will evaluate to to `True`, which is treated as equivalent to the number 1 in python. So to create the number 2 we can do:

```python3
>>> ([] <= []) + ([] <= [])
2
```

Of course, now we can obtain any number we want. To make our lives a little easier we can quickly generate all powers of two between 1 and 128 and store them in variables and then combine these variables to produce any positive integer less than 256. Of course, our variable names all need to be composed of f's and \_'s.

```python3
ff        = [] <= []
fff       = ff       << ff
ffff      = fff      << ff
fffff     = ffff     << ff
ffffff    = fffff    << ff
fffffff   = ffffff   << ff
ffffffff  = fffffff  << ff
fffffffff = ffffffff << ff
```

## Regaining Letters

Here we encounter our first major difficulty. To produce strings from objects the writeup uses another trick: the ```x``` operator 
