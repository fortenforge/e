---
layout: post
title:  "Blaze CTF 2017: shellcodeme (420 pts)"
date:   2018-04-23 3:12:44 -0500
categories: ctfs pwn
---

This was a simply-posed but tricky pwnable that [ghostly_gray](https://raywang.tech) and I solved over the course of a long plane ride. We were given the [source code]({{ site.baseurl }}/assets/blazectf2018/shellcodeme/shellcodeme.c) (shown below) as well as the [binary]({{ site.baseurl }}/assets/blazectf2018/shellcodeme/shellcodeme). Simply put, we had to write some x86-64 shellcode that used no more than 7 distinct bytes.

<!--more-->

```c
// gcc -zexecstack -Os shellcodeme.c -o shellcodeme
#include <stdio.h>
#include <string.h>
#include <sys/mman.h>
#include <unistd.h>

#define BUF_SIZE (0x4096 & ~(getpagesize()-1))

int main() {
    setbuf(stdout, NULL);
    unsigned char seen[257], *p, *buf;
    void (*f)(void);
    memset(seen, 0, sizeof seen);
    buf = mmap(0, BUF_SIZE, PROT_READ | PROT_WRITE | PROT_EXEC, MAP_PRIVATE | MAP_ANONYMOUS, -1, 0);
    puts("Shellcode?");
    fgets(buf, BUF_SIZE, stdin);
    fflush(stdin);
    for(p=buf; *p != '\n'; p++) {
        seen[256] += !seen[*p];
        seen[*p] |= 1;
    }
    if(seen[256] > 7) {
        puts("Shellcode too diverse.");
        _exit(1);
    } else {
        *(void**)(&f) = (void*)buf;
        f();
        _exit(0);
    }
}
```

## Initial Work

We had no clue whether ASLR was on or off, and `system` wasn't anywhere to be found in the binary, so our first approach was to make a syscall to `execve` with "/bin/sh" as an argument.

This went... poorly. Despite our best efforts we couldn't manage to do this with less than 11 distinct bytes. The problem was there were simply too many steps involved with setting up the syscall. We had to:
* Push `0x68732f6e69622f2f` onto the stack ("/bin/sh")
* Point `rdi` towards it (first argument)
* Set `rax` to 59 (syscall number for `execve`)
* Set `rsi` and `rdx` to 0 (2nd and 3rd arguments need to be NULL)
* Make the syscall (2 byte instruction)

In the process of struggling, we did come up with a fairly efficient way of getting an arbitrary value in a register. The two instructions `inc eax` and `add eax, eax` use only three unique bytes between them and allow you to increment or double the value in the `eax` register. By following the binary representation of a number X from left to right, we can repeatedly apply these two instructions to set `eax` to X using only a logarithmic number of instructions.

For example, to set `eax` to 13 = 0b1101, we apply:

```
inc eax
add eax, eax
inc eax
add eax eax
add eax eax
inc eax
```


doubling after every bit, and incrementing after every bit that is a 1. (This is reminiscent of exponentiation by squaring or the [double-and-add](https://en.wikipedia.org/wiki/Elliptic_curve_point_multiplication#Double-and-add) elliptic curve point multiplication algorithm).

Still, with only 4 bytes left to spare, we couldn't figure out how to call `syscall` and set the values of four different registers. So, we started looking for tricks.

I began trying to overflow the value in `seen[256]` which keeps track of the number of distinct characters. I reasoned that if we sent some shellcode that used all 256 different bytes, it would overflow to 0 and pass the "< 7" check. Alas, it was impossible to send a "\n" character without terminating the `fgets`, so the maximum value of `seen[256]` seemed to be 255.

However, this did lead us to a working strategy: use our shellcode to modify the value of `seen[256]` to something large and then jump back earlier in `main` (after the `memset`) to redo the `fgets`. Then send some standard, diverse shellcode which will overflow `seen[256]` to something less than 7.

## The Attack

In the assembly, `seen[256]` was located at `rsp + 0x107`. Here was our strategy:
* Set `eax` to 0x107 using the double-and-add trick
* Call `mov BYTE PTR[rsp + rax], 240`
* Set `eax` to 0x40069b (the start of the `mmap` call) using the double-and-add trick
* Call `jmp rax`
* Send some standard shellcode in the second call to `fgets`
* Win

Critically, we had to construct the first payload to use no more than 7 distinct bytes. Our super-efficient double-and-add routine made this feasible. Below we've shown the four assembly instructions we used and their assembled byte sequences.

| Instruction                    | Assembly           |
| ------------------------------ | ------------------ |
| `inc eax`                      | `\xff\xc0`         |
| `add eax, eax`                 | `\x01\xc0`         |
| `mov BYTE PTR[rsp + rax], 240` | `\xc6\x04\x04\xf0` |
| `jmp rax`                      | `\xff\xe0`         |

Our standard shellcode uses 19 distinct bytes, so this results in `seen[256]` having a value of 240 + 19 = 259 == 3 < 7.

You can read our full exploit script [here]({{ site.baseurl }}/assets/blazectf2018/shellcodeme/exploit.py). Running it against the server pops a shell, letting us read the flag: `blaze{g0lf3d_y0ur_sh3llc0d3's_un1qu3_byt3z}`.

![winning]({{ site.baseurl }}/assets/blazectf2018/shellcodeme/winning.png)
