---
layout: post
category: Malware
tags: [hacking, programming, malwaredev]
---

## Introduction

This blog post gives an overview of three types of Linux rootkits,
ordered by their complexity.

## Definitions
What is a rootkit? A rootkit is a program that is installed on a
computer after it is breached. It allows the attacker to maintain a
foothold on the system. Thus, it is a mechanism for achieving
persistence. Consequently it should be stealthy, should survive
reboots, and should offer a backdoor, so the attacker can again
execute commands on the machine without repeating the steps that were
taken in the initial compromise.

In the Linux world there are three main types of rootkits:
- Simple Daemons
- LD_PRELOAD Rootkits
- Kernel Rootkits

## 1. Simple Daemons
Daemons are probably the easiest types of rootkits on Linux systems.
They are simple jobs that are configured within the init system, such
that they are started on boot. They have the form of an executable
that is executed. An example is a systemd daemon, or an entry in
`/etc/rc.local`.

Even though they are the easiest to program, they are also the easiest
to detect.
On this level, they can hardly offer any evasion capabilities.
Consequently they can be found in the list of running processes, even
though they may hide under a name that does not look suspicious.

## 2. LD_PRELOAD Rootkits
These rootkits are more complex to write than simple daemons. They are
in the form of a library that is preloaded.
As an example, if they are registered within `/etc/ld.preload.so`,
they are preloaded for any other executable in user space.

These types of rootkits can offer library hooking functionalities in
order to hook functions in libc. As an example, it is trivially
possible to hook a function such as `open()`, so that it hides files
with special filenames. This way, the library can hide itself,
and the existence of `/etc/ld.preload.so`.

A minimal viable LD_PRELOAD rootkit hooks at least `stat()`, `open()`,
`opendir()`, `readdir()`, `unlink()` and its 64bit counterparts.

Rootkits making use of this technique are fairly easy to maintain,
as they are compatible with lots of different versions of libc and
lots of different distributions.

The sophistication of their evasion capabilities is between those of simple daemons and
kernel rootkits. Hooking library calls can hide then from common tools
such as `ls`, `cat`, `vim`, `ps` and others. However, if one decides
to search for them, they can still be detected.

As they only hook
dynamically linked executables, they can be found using statically
linked binaries, such as busybox.
Another way to detect them is dumping the file system using `dd` and
restoring the index and comparing that index to the output of a
suitable `find` command.
A third way of detection is by searching for the LD_PRELOAD environment
variable. If the rootkit performs `unsetenv()`, the environment
variable LD_PRELOAD is hidden from the command `env`.
This can be achieved by executing `unsetenv()` in the constructor of
the library, so that it is executed when the library loads. For this
purpose, when using gcc it suffices to prefix the function with
`__attribute__((constructor))`.
However, the environment variable is still persistent in the mapping
of the binary in memory, and thus can be detected, e.g. by executing
`cat /proc/self/environ`.

If you are interested in source code of LD_PRELOAD rootkits, you can
take a look at the following links.

- [azazel](https://github.com/chokepoint/azazel)
- [Jynx2](https://github.com/chokepoint/Jynx2)
- [jynxkit](https://github.com/chokepoint/jynxkit)
- [vlany](https://github.com/mempodippy/vlany)
- [Umbreon-Rootkit](https://github.com/NexusBots/Umbreon-Rootkit)
- [rkorova](https://github.com/nopn0p/rkorova)
- [beurk](https://github.com/unix-thrust/beurk)

## 3. Kernel Rootkits
The main problem with LD_PRELOAD rootkits is that they still reside in
user space and have to hide from other user space programs.
Consequently their stealthiness is limited.

While LD_PRELOAD rootkits can only hook other library functions,
kernel rootkits are able to hook syscalls. In user space it is
theoretically also possible to hook syscalls using the ptrace
syscall, but that is laborious and has to be performed for each
process.

Consequently it makes sense to install a rootkit inside kernel space.
Beyond hooking syscalls and thus outsmarting statically and dynamically
linked binaries, kernel rootkits can also modify the list of processes
that are managed by the kernel in order to stay undetected. However,
installing a kernel rootkit is more invasive compared to an LD_PRELOAD
rootkit and may thus be easier to detect. Still, once installed, a
sophisticated kernel rootkit is hard to detect, especially if the
detection mechanisms reside in user space.

A major drawback of kernel rootkits is that they have to be targeted
towards specific versions of kernels. If a new kernel is released,
then the rootkit may not work anymore, as some functions are not
available anymore. In particular, around kernel 2.6, the calling
convention for functions changed. Consequently, maintaining reliable
code for a kernel rootkit is a more extensive task.

If you are interested in kernel rootkits, a good candidate to study is
Drovorub (see [its excellent analysis by xcellerator](https://xcellerator.github.io/posts/linux_rootkits_10/) or [the official NSA/FBI advisory](https://media.defense.gov/2020/Aug/13/2002476465/-1/-1/0/CSA_DROVORUB_RUSSIAN_GRU_MALWARE_AUG_2020.PDF)).
This is a rootkit by the Russian APT group Fancy Bear. It has a kernel
and a user space component.
The kernel component hooks some syscalls to remove processes, files,
and network sockets that would indicate its presence. The user space
component is used to communicate with the kernel component, by writing
commands to `/dev/null`. As an example these commands can hide network
connections or files. The output of these commands can again be read
from `/dev/null`.

While the functionality of this rootkit may not seem impressive and
can probably be recreated within some weeks, the hard part is to
maintain compatibility with different kernel versions.
