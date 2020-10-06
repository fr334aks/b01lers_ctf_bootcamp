# Goodbye, Mr Anderson

> **Descpription**: Do it again Noe, Cheat Death
>
> **Points**: 300
>
>`nc chal.ctf.b01lers.com 1009`

## Challenge Overview
This challenge's source code was provided together with its libc

```
#include <stdio.h>
#include <stdlib.h>

char name[16];

void yay() {
    asm("pop %rax");
    asm("syscall");
    return;
}

char * leak_stack_canary(char * buffer, int maxlen) {
    int length;

    scanf("%d", &length);
    if (length > maxlen) {
        exit(13);
    }

    fgetc(stdin);

    for (int i = 0; i <= length; i++) {
         buffer[i] = fgetc(stdin);
    }

    return buffer;
}

int main() {
    setvbuf(stdout, 0, 2, 0);
    setvbuf(stderr, 0, 2, 0);

    char buffer[24];

    printf("You hear that, Mr. Anderson? That's the sound of inevitability, that's the sound of your death, goodbye, Mr. Anderson.\n");

    leak_stack_canary(name, 16);

    leak_stack_canary(buffer, 64);
    printf("%s\n", buffer);
    leak_stack_canary(buffer, 64);
    printf("%s\n", buffer);
    leak_stack_canary(buffer, 128);
    printf("%s\n", buffer);
}

```

Looking at the source code we can see the vulnerable function `leak_stack_canary` that enables us write passed the buffer that is 24 bytes.
All protections are enabled. `PIE`, `Stack canary` ,`NX` =(. We can leak addresses using  `printf` we all know that `printf` reads a string
until it enconters a `null byte`. This can therefore enable us leak the stack canary, and some other addresses from the stack to get the base address
of the binary since `PIE` is enabled.

## Exploit Overview

We have `leak_canary_function` that runs four times. I used the second `leak_stack_canary` to leak an address
from the stack that turned out to be `_start`. With this we can get the base address of the binary.
I used the second to leak the `stack_canary` and the third to control `RIP`.

Using `ROP` we can now leak the `libc_address`since we have `RIP` controlled and since `libc` is already provided, we then find the address of `system` and `/bin/sh`.and therefore we can now spawn a shell =). The final exploit [exploit.py](exploit.py)

## Flag

`flag{l0tsa_l33ks_4r3_imp0rt4nt}`
