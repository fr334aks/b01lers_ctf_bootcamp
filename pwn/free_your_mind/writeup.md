# Free Your Mind
> **Points**: 200
>
> **Description**: Next up, hack the matrix again, but this time, insert your own code.
>
> `nc chal.ctf.b01lers.com 100`

## Challenge Overview
The source code was provided and it was a follows:

```
#include <stdio.h>
#include <unistd.h>

char shellcode[16];

int main() {
    char binsh[8] = "/bin/sh";

    setvbuf(stdout, 0, 2, 0);
    setvbuf(stderr, 0, 2, 0);

    printf("I'm trying to free your mind, Neo. But I can only show you the door. You're the one that has to walk through it.\n");
    read(0, shellcode, 16);

    ((void (*)()) (shellcode))();
}

```
Looking at the source code and according to the description, all we had to do was to provided a `shellcode` since the binary has `nx` disabled. 
`/bin/sh` is already provided and will be located on the stack making shellcode generation easier.

A quick note the shellcode should be less than `16 bytes` because of `read(0, shellcode, 16)`.

## Exploit Overview
using `pwntools asm` we will generate the shellcode and run it to get a shell. Check out the final [exploit](exploit.py)
## Flag
`flag{cust0m_sh3llc0d1ng_c4n_b33_c00l}`
