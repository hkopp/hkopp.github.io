---
layout: post
category : Computer Trick
tags : [bash,programming]
---

## Intro
This is a short list of useful intermediate bash tricks.
Often, the problem with this kind of posts in my opinion is that some
tricks are explained, but it is not made clear when to use them.
Thus, I try to introduce these techniques using a problem.
I will discuss command substitution, process substitution and the
heredoc syntax.

## Command Substitution
### Problem
Say, I want to find out which libraries the command cat uses.
Determining the libraries can be done using the command *ldd*.
However, ldd takes a file as an argument.
Consequently I have to find out where cat is and then invoke ldd on
it.

{% highlight Bash %}
hk@localhost:~$ which cat
/usr/bin/cat
hk@localhost:~$ ldd /usr/bin/cat
	linux-vdso.so.1 (0x00007fff944f0000)
	libc.so.6 => /lib64/libc.so.6 (0x00007fe7d6b11000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fe7d70da000)
{% endhighlight %}
That is far too much to type.

### Solution
Bash can substitute a command by its result if it is between
backticks. A more modern approach is writing the command $(like this).
As we do not care where cat lives, we can use the following code.
{% highlight Bash %}
hk@localhost:~$ ldd $(which cat)
	linux-vdso.so.1 (0x00007ffd8e9e8000)
	libc.so.6 => /lib64/libc.so.6 (0x00007f6975f48000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f6976511000)
{% endhighlight %}
In general, Bash substitutes the string $(command) by the result of invoking *command*.

## Process Substitution
### Problem
The same can be done using processes. Say, we want to base64 encode
the string "Hallo". Further, we do not know that *base64*
can read from standard input when given `-` as filename.
Consequently, as base64 reads from a file, we would have to write
"Hallo" to a file and then base64 encode it.
{% highlight Bash %}
hk@localhost:~$ echo "Hallo" > temp
hk@localhost:~$ base64 temp
SGFsbG8K
hk@localhost:~$ rm temp
{% endhighlight %}
Argh! What a waste. Why can't we use a temporary file which deletes
itself?
### Solution
Well, it turns out we can.
With the syntax <(command) bash gives us a temporary file which
contains the result of running *command*.
Thus, we can do the following:
{% highlight Bash %}
hk@localhost:~$ base64 <(echo "Hallo")
SGFsbG8K
{% endhighlight %}
What happens here is that Bash generates a temporary file descriptor.
{% highlight Bash %}
hk@localhost:~$ echo <(echo "Hallo")
/dev/fd/63
{% endhighlight %}
This is a symbolic link to a pipe.
{% highlight Bash %}
hk@localhost:~$ file <(echo "Hallo")
/dev/fd/63: broken symbolic link to pipe:[3115050]
{% endhighlight %}
Don't ask me why it says that it is broken. Perhaps due to some race
condition.

## Heredoc
### Problem
You are on a server and do not have a nice text editor. Say, you are
in a reverse shell. Nevertheless, you have a multi-line kernel exploit
which you want to use.
### Solution
{% highlight Bash %}
hk@localhost:~$ cat > file.txt <<EOF
> first line
> second line
> third line
> EOF
{% endhighlight %}
This reads up to the string EOF, and puts that stuff into file.txt.
{% highlight Bash %}
hk@localhost:~$ cat file.txt
first line
second line
third line
{% endhighlight %}
