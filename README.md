
# Not So Simple — CTF Writeup

## Introduction
> Hello, my name is Abdallah Ghanim, and I’m excited to share my walkthrough of my first CTF, where I solved a Reverse Engineering challenge. Let’s get started.
----------
## Description

> Someone tried to hide the password inside the binary, but they weren't smart enough... The key is right there, just not in plain sight.

We're given a binary file: `not_so_simple`

----------

## First Step

The description hints that a password is hidden inside the binary but not immediately visible. A natural first instinct is to run `strings` on the binary:

```bash
strings not_so_simple

```

The output is C-like decompiled code.

----------

## Code Analysis

Viewing the C-like decompiled output in a reverse engineering tool.

```c
int check_password(char *input) {
    unsigned char key[] = {
        0x00, 0x10, 0x09, 0x01, 0x1b, 0x11, 0x39,
        0x3a, 0x72, 0x30, 0x1d, 0x73, 0x31, 0x1d,
        0x2c, 0x72, 0x36, 0x1d, 0x31, 0x71, 0x21,
        0x37, 0x30, 0x71, 0x3f
    };

    int len = strlen(input);
    if (len != 25) return 0;

    for (int i = 0; i < 25; i++) {
        if ((input[i] ^ 0x42) != key[i]) return 0;
    }
    return 1;
}

```

The logic is straightforward:

1.  The input must be exactly **25 characters** long.
2.  Each character of the input is **XORed with `0x42`**.
3.  The result is compared against a **hardcoded key array**.

If all 25 bytes match, the flag is correct.

----------

## Solving It

Since XOR is its own inverse, we can recover the flag by simply XORing each byte of the key back with `0x42`:

```
flag[i] = key[i] ^ 0x42

```
We can use an online XOR calculator to compute each byte by XORing it with 0x42.

Example on first byte 

```
flag[0] = 0x00 ^ 0x42 = B

```

![XOR calculation example](XOR%20example.png)

and similarly for the remaining 24 bytes.

**Flag:**

```
BRKCYS{x0r_1s_n0t_s3cur3}

```
