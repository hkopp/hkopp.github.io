---
layout: post
category : Walkthrough
tags : [hacking, ctf, spoiler, bandit, writeup]
---

## Intro
Some time ago I decided that it is time to brush up my computer
security skills.
I went to [http://overthewire.org](http://overthewire.org), which is a site with hacking
challenges and did some of those. I did their simplest game, called
Bandit. Some of the puzzles were too basic for me, some of
them included researching obscure functions in manpages.
So I did not finish the challenges, since my motivation left.
I had some writeups of the levels and thought that I should not
publish them, since they are not complete. They lay around in my
drafts-folder for a year and since I do not think I will ever finish
them, I will now publish them.
Have fun reading.

## Level 0
So I went to this site and it said that I should ssh to
bandit.labs.overthewire.org with password bandit0 and username bandit0

    h@ophit:~$ ssh bandit0@bandit.labs.overthewire.org
    The authenticity of host 'bandit.labs.overthewire.org (178.79.134.250)' can't be established.
    ECDSA key fingerprint is 05:3a:1c:25:35:0a:ed:2f:cd:87:1c:f6:fe:69:e4:f6.
    +--[ECDSA  256]---+
    |    ..oo=        |
    |     o.= o       |
    |     .=   .      |
    |      ..o.       |
    |       *S+       |
    |      . * o .    |
    |       . o o     |
    |          . +.   |
    |           +o.E  |
    +-----------------+
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'bandit.labs.overthewire.org,178.79.134.250' (ECDSA) to the list of known hosts.
    
    This is the OverTheWire game server. More information on http://www.overthewire.org/wargames
    
    Please note that wargame usernames are no longer level<X>, but wargamename<X>
    e.g. vortex4, semtex2, ...
    
    Note: at this moment, blacksun is not available.
    
    bandit0@bandit.labs.overthewire.org's password: 
                   
          ,----..            ,----,          .---. 
         /   /   \         ,/   .`|         /. ./|
        /   .     :      ,`   .'  :     .--'.  ' ;
       .   /   ;.  \   ;    ;     /    /__./ \ : |
      .   ;   /  ` ; .'___,/    ,' .--'.  '   \' .
      ;   |  ; \ ; | |    :     | /___/ \ |    ' ' 
      |   :  | ; | ' ;    |.';  ; ;   \  \;      : 
      .   |  ' ' ' : `----'  |  |  \   ;  `      |
      '   ;  \; /  |     '   :  ;   .   \    .\  ; 
       \   \  ',  /      |   |  '    \   \   ' \ |
        ;   :    /       '   :  |     :   '  |--"  
         \   \ .'        ;   |.'       \   \ ;     
      www. `---` ver     '---' he       '---" ire.org     
                   
                  
    Welcome to the OverTheWire games machine!
    
    If you find any problems, please report them to Steven on
    irc.overthewire.org.
    
    --[ Playing the games ]--
    
      This machine holds several wargames. 
      If you are playing "somegame", then:
    
        * USERNAMES are somegame0, somegame1, ...
        * Most LEVELS are stored in /somegame/.
        * PASSWORDS for each level are stored in /etc/somegame_pass/.
    
      Write-access to homedirectories is disabled. It is advised to create a
      working directory with a hard-to-guess name in /tmp/.  You can use the
      command "mktemp -d" in order to generate a random and hard to guess
      directory in /tmp/.  Read-access to both /tmp/ and /proc/ is disabled
      so that users can not snoop on eachother.
    
      Please play nice:
          
        * don't leave orphan processes running
        * don't leave exploit-files laying around
        * don't annoy other players
        * don't post passwords or spoilers
        * again, DONT POST SPOILERS! 
          This includes writeups of your solution on your blog or website!
    
    --[ Tips ]--
    
      This machine has a 64bit processor and many security-features enabled
      by default, although ASLR has been switched off.  The following
      compiler flags might be interesting:
    
        -m32                    compile for 32bit
        -fno-stack-protector    disable ProPolice
        -Wl,-z,norelro          disable relro 
    
      In addition, the execstack tool can be used to flag the stack as
      executable on ELF binaries.
    
      Finally, network-access is limited for most levels by a local
      firewall.
    
    --[ Tools ]--
    
     For your convenience we have installed a few usefull tools which you can find
     in the following locations:
    
        * peda (https://github.com/longld/peda.git) in /usr/local/peda/
        * gdbinit (https://github.com/gdbinit/Gdbinit) in /usr/local/gdbinit/
        * pwntools (https://github.com/Gallopsled/pwntools) in /usr/src/pwntools/
        * radare2 (http://www.radare.org/) should be in $PATH
    
    --[ More information ]--
    
      For more information regarding individual wargames, visit
      http://www.overthewire.org/wargames/
    
      For questions or comments, contact us through IRC on
      irc.overthewire.org.
    
    
    bandit0@melinda:~$ 

Don't post spoilers? Well, how can an individual interested in security learn
about all this stuff if there are no instructions anywhere? But in order to
honor the creators of the challenge, I am going to redact the passwords.

    bandit0@melinda:~$ l
    readme
    bandit0@melinda:~$ cat readme
    <secret password>

Look what we have here: A nice password for the next level.

## Level 1
With the password we can access the next level.

    hjgk@ophit:~$ ssh bandit1@bandit.labs.overthewire.org
    bandit1@bandit.labs.overthewire.org's password: 
    bandit1@melinda:~$ l
    -

What is this? A file with a funny name? Indeed.

    bandit1@melinda:~$ ls -la
    total 24
    -rw-r-----   1 bandit2 bandit1   33 Nov 14  2014 -
    drwxr-xr-x   2 root    root    4096 Nov 14  2014 .
    drwxr-xr-x 167 root    root    4096 Jul  9 16:27 ..
    -rw-r--r--   1 root    root     220 Apr  9  2014 .bash_logout
    -rw-r--r--   1 root    root    3637 Apr  9  2014 .bashrc
    -rw-r--r--   1 root    root     675 Apr  9  2014 .profile
    bandit1@melinda:~$ cat ./-   
    <secret password>

And up to the next level.

## Level 2
Again logging in with ssh.

    bandit2@melinda:~$ l
    spaces in this filename
    bandit2@melinda:~$ cat spaces\ in\ this\ filename
    <secret password>

At this time I wondered if it is a mistake that bash-completion is enabled.
Well... Let's check the next one.

## Level 3

    bandit3@melinda:~$ l
    inhere/
    bandit3@melinda:~$ less inhere/.hidden 
    bandit3@melinda:~$ cat inhere/.hidden

Piece of cake.

## Level 4

    bandit4@melinda:~$ l                 
    inhere/
    bandit4@melinda:~$ cd inhere/
    bandit4@melinda:~/inhere$ l
    -file00  -file01  -file02  -file03  -file04  -file05  -file06  -file07  -file08  -file09
    bandit4@melinda:~/inhere$ cat *
    cat: invalid option -- 'f'
    Try 'cat --help' for more information.
    bandit4@melinda:~/inhere$ cat -file0
    cat: invalid option -- 'f'
    Try 'cat --help' for more information.
    bandit4@melinda:~/inhere$ cat ./*
    <many funny signs><something that looks like a password>
    bandit4@melinda:~/inhere$ grep <piece of the password> ./*
    ./-file07:<secret password>

That was more fun, but I am starting to wonder if I am the right target group.
Come on, I am not a shell-noob. Where are the code injections?

## Level 5
This level is more fun. We have many files scattered around many
folders. Too much to look at all of them. We assume that the flag
resides in a file with only one line and contains only
ascii-characters, like in the previous levels.
So we list all files and their corresponding line numbers
```
bandit5@melinda:~$ find . * -exec wc -l {} \;
```
Some of them contain only one line, so it seems we are on the right
track. We are only interested in the one-liners, so we search with
grep if the output contains "1 ". The space is important to exclude
files with 13 or 17 lines. Then, we strip the "1 " away, such that
only the filenames remain.

    bandit5@melinda:~$ find . * -exec wc -l {} \;|grep "^1 "|sed 's/1 //'

Now I want to know which of those are ASCII.
Unfortunately some of the files contain spaces. So later mangling is
difficult. We have to think of another way. We could first look which
are ASCII and then pick the small ones.

Let us start by addressing the many errors that occur when find yields
a file and wc -l is invoke on a file. The find command has the switch
-t which allows to determine the type of the match. We want files, so
we add '-t f' to find.    
Also we want the delimiters to be different. File has the flag
--print0 to use zero-characters as delimiters. These are not
ASCII-zeros but binary zeros, like those used in delimiting strings in
C. xargs has a corresponding option -0 to also use zero-characters as
delimiters.

    bandit5@melinda:~$ find . -type f -print0 |xargs -0 file

Now we can pick the ASCII-files. The command 'file' sometimes says
that a file is "ASCII text, with very long lines". We probably do not
want to have them.

    bandit5@melinda:~$ find . -type f -print0 |xargs -0 file|grep ASCII|grep -v "very long"

Seven files remain which we can check by hand.
But, surprise, all the strings are horribly long and do not look like
passwords. That's when I came to the hints on the website of the
bandit-wargame. There it says that the file is human-readable, has
1033 bytes and is not executable. Damn. We threw the password away
when throwing everything away which had long lines.

So. Back again. We are now searching for a file with exactly 1033
bytes.

    bandit5@melinda:~$ find . -type f -print0 |xargs -0 ls -la|grep 1033

Bam! Got the password. Next level!

## Level 6
When connecting to bandit.labs.overthewire.org as bandit6 we see that
the home directory is empty. The hint on the website says that the
password is stored somewhere on the server, is owned by user bandit7,
owned by group bandit6, and is 33 bytes in size.

    bandit6@melinda:~$ cd /
    bandit6@melinda:/$ find . -type f -print0|xargs -0 ls -la|grep bandit6|grep bandit7|grep 33

Probably not how they thought I should solve this, but effective.
After pressing enter I see a file which contains the password. Nice
challenge.

## Level 7
So, on to level 7.

In the home-directory we have a file called data.txt with too many
lines to read them all. Each lines contains a pair of a name and
something that looks like a password. The hint is that the password is
stored next to the word "millionth".

    bandit7@melinda:~$ grep millionth data.txt 
    millionth <secret password>

Babing!

## Level 8
Level 8 is really similar to level 7. We have a file called data.txt
and the password is the only line that occurs only once.

    bandit8@melinda:~$ uniq -c data.txt |sort -n

```uniq -c``` counts the occurences of the lines. ```sort -n``` sorts
the lines as if they were numbers, so not lexicographically.
And well, it does not work. uniq only deletes adjacent duplicate
lines. So, first we have to sort the lines, then delete the ajacent
duplicate lines an prepend them with their number of occurences.
Lastly, we sort again, but reverse, so that the smallest number of
occurences is at the bottom.

    sort data.txt |uniq -c|sort -nr

Works!

## Level 9
Level 9 is again a data.txt. We are searching the only human-readable string
which starts with a number of equal signs.
The command strings dumps the human readable strings and then we have
to grep for the equal sign.

    bandit9@melinda:~$ strings data.txt |grep ==
    I========== the6
    ========== password
    ========== ism
    ========== <secret password>

Hmpf! That was easy.

## Level 10
I log in and again i have this data.txt. This time it is
base64-encoded.
So i have to decode it. There is a tool for this surprisingly called
```base64```.
There is the flag -d for decoding.
So all i have to do is the following.

    bandit10@melinda:~$ base64 -d data.txt 
    The password is <secret password>

Done! Up to the next one.

## Level 11
Oh no. Not again a data.txt. I increase the volume of my earphones
slightly. What is it this time?
Looks like a string which is somehow encrypted.
The hint at the overthewire-site says that all characters are rotated
by 13 positions. Well, sounds an awful lot like rot13.

Fortunately there is a tool called ```tr``` which can substitute
characters by other characters.

    bandit11@melinda:~$ cat data.txt | tr '[A-Za-z]' '[N-ZA-Mn-za-m]'
    The password is <secret string>

Does this constitute a useless use of cat?

    bandit11@melinda:~$ tr '[A-Za-z]' '[N-ZA-Mn-za-m]' data.txt 
    tr: extra operand 'data.txt'
    Try 'tr --help' for more information.

.....Probably not.

## Level 12
Again we have a data.txt.
This time it looks a lot like hex.

    bandit12@melinda:~$ head data.txt 
    0000000: 1f8b 0808 34da 6554 0203 6461 7461 322e  ....4.eT..data2.
    0000010: 6269 6e00 013f 02c0 fd42 5a68 3931 4159  bin..?...BZh91AY
    0000020: 2653 5982 c194 8a00 0019 ffff dbfb adfb  &SY.............
    0000030: bbab b7d7 ffea ffcd fff7 bfbf 1feb eff9  ................
    0000040: faab 9fbf fef2 fefb bebf ffff b001 3b18  ..............;.
    0000050: 6400 001e a000 1a00 6468 0d01 a064 d000  d.......dh...d..
    0000060: 0d00 0034 00c9 a320 001a 0000 0d06 80d1  ...4... ........
    0000070: a340 01b4 98d2 3d13 ca20 6803 40d1 a340  .@....=.. h.@..@
    0000080: 1a00 0340 0d0d 0000 000d 0c80 6803 4d01  ...@........h.M.
    0000090: a3d4 d034 07a8 0683 4d0c 4034 069e 91ea  ...4....M.@4....

A direct ```xxd -r``` which casts it back to binary does not work this
time. So i read the hint at the website. It is a file that has been
compressed repeatedly. Well. Nothing easier than that.
First we try to create a separate directory somewhere in /tmp

    bandit12@melinda:~$ mkdir /tmp/a
    mkdir: cannot create directory '/tmp/a': File exists
    bandit12@melinda:~$ mkdir /tmp/mydir
    mkdir: cannot create directory '/tmp/mydir': File exists
    bandit12@melinda:~$ mkdir /tmp/s    
    mkdir: cannot create directory '/tmp/s': File exists

Damn. I have to think of a more difficult name.

    bandit12@melinda:~$ mkdir /tmp/decompressdir
    bandit12@melinda:~$ cp data.txt /tmp/decompressdir
    bandit12@melinda:~$ cd /tmp/decompressdir

Let us hope i can remember this one.

    bandit12@melinda:/tmp/decompressdir$ xxd -r data.txt > a
    bandit12@melinda:/tmp/decompressdir$ file a
    a: gzip compressed data, was "data2.bin", from Unix, last modified: Fri Nov 14 10:32:20 2014, max compression

So the first layer is a gzip-file containing some data.bin.

    bandit12@melinda:/tmp/decompressdir$ gunzip a 
    gzip: a: unknown suffix -- ignored

Stupid gunzip. It looks at the file-ending and is not convinced that
the file a is indeed gzipped. We can change that.

    bandit12@melinda:/tmp/decompressdir$ cp a a.gz
    bandit12@melinda:/tmp/decompressdir$ gunzip a.gz 
    gzip: a already exists; do you wish to overwrite (y or n)? y
    bandit12@melinda:/tmp/decompressdir$ l
    a  data.txt

Seemed to work.

    bandit12@melinda:/tmp/decompressdir$ file a 
    a: bzip2 compressed data, block size = 900k

Yep. Worked. The file is now a different one.

    bandit12@melinda:/tmp/decompressdir$ bunzip2 a
    bunzip2: Can't guess original name for a -- using a.out

Yeah. Whatever. As long as you tell me how you name it.

    bandit12@melinda:/tmp/decompressdir$ file a.out 
    a.out: gzip compressed data, was "data4.bin", from Unix, last modified: Fri Nov 14 10:32:20 2014, max compression
    bandit12@melinda:/tmp/decompressdir$ cp a.out a.gz
    bandit12@melinda:/tmp/decompressdir$ gunzip a.gz 
    bandit12@melinda:/tmp/decompressdir$ l
    a  a.out  data.txt

Peeling away another layer.

    bandit12@melinda:/tmp/decompressdir$ file a
    a: POSIX tar archive (GNU)
    bandit12@melinda:/tmp/decompressdir$ tar xvf a
    data5.bin

Nice challenge. I guess now we have gone through all compression tools
except cpio.

    bandit12@melinda:/tmp/decompressdir$ file data5.bin 
    data5.bin: POSIX tar archive (GNU)
    bandit12@melinda:/tmp/decompressdir$ tar xvf data5.bin
    data6.bin
    bandit12@melinda:/tmp/decompressdir$ file data6.bin 
    data6.bin: bzip2 compressed data, block size = 900k

At this point perhaps I thought that I should perhaps write a tool to
automate the decompression process. But I persisted.

    bandit12@melinda:/tmp/decompressdir$ bunzip2 data6.bin
    bunzip2: Can't guess original name for data6.bin -- using
    data6.bin.out
    bandit12@melinda:/tmp/decompressdir$ file data6.bin.out 
    data6.bin.out: POSIX tar archive (GNU)
    bandit12@melinda:/tmp/decompressdir$ tar xvf data6.bin.out
    data8.bin
    bandit12@melinda:/tmp/decompressdir$ file data8.bin 
    data8.bin: gzip compressed data, was "data9.bin", from Unix, last modified: Fri Nov 14 10:32:20 2014, max compression
    bandit12@melinda:/tmp/decompressdir$ cp data8.bin data8.bin.gz
    bandit12@melinda:/tmp/decompressdir$ gunzip data8.bin.gz 
    gzip: data8.bin already exists; do you wish to overwrite (y or n)? y
    bandit12@melinda:/tmp/decompressdir$ file data8.bin 
    data8.bin: ASCII text

Sigh! Finally! And yes. The file contained the password for the next
level.

## Level 13
I log into the box and this time there is no data.txt. What a relief.
Instead there is a sshkey.private.
Well..... Level solved, right?

SSH-keys are keys for asymmetric encryption which i use daily. To
simplify. This is like a password.
The hint on the website says that the password is stored in
/etc/bandit_pass/bandit14 and can only be read by user bandit14. So.
Let's log in as bandit14 with the private key and get the password.

    bandit13@melinda:~$ ssh -i sshkey.private bandit14@localhost
    Could not create directory '/home/bandit13/.ssh'.
    The authenticity of host 'localhost (127.0.0.1)' can't be established.
    ECDSA key fingerprint is 05:3a:1c:25:35:0a:ed:2f:cd:87:1c:f6:fe:69:e4:f6.
    Are you sure you want to continue connecting (yes/no)? yes

Obviously it does not know the fingerprint of the machine I am
connecting to. Normally it should copy it to .ssh/known_hosts so that it
knows the other machine the next time. In this case, the permissions
are set such that the copying does not take place and the next player
will also have his fun.

    bandit14@melinda:~$ cat /etc/bandit_pass/bandit14
    <secret key>

Bingo!

## Level 14
This time the hint is that I should submit the current passwort on port 30000 on localhost.
So I log in on the machine. The next step is establishing a connection
to itself, i. e., localhost.
You can think of port 30000 as an identifier so that the computer does
not mix up different connections to the same machine.

    bandit14@melinda:~$ telnet localhost 30000
    Trying 127.0.0.1...
    Connected to localhost.
    Escape character is '^]'.
    <current password>
    Correct!
    <new secret password>

    Connection closed by foreign host.

Thanks.

## Level 15
The hint says that we first have to find out the correct port on
localhost which is somewhere between 31000 and 32000.
At first I tried `netstat -tulpen`, but there were many listening ports and the
info which process was listening was missing, so I tried nmap.

    bandit15@melinda:~$ nmap -A -p 31000-32000 127.0.0.1

    Starting Nmap 6.40 ( http://nmap.org ) at 2016-01-17 14:23 UTC
    Nmap scan report for localhost (127.0.0.1)
    Host is up (0.00080s latency).
    Not shown: 995 closed ports
    PORT      STATE SERVICE VERSION
    31000/tcp open  ssh     (protocol 2.0)
    | ssh-hostkey: 1024 23:51:06:ec:31:58:1e:64:10:51:58:9c:92:6f:08:55
    (DSA)
    | 2048 85:e5:7b:e2:87:74:18:c5:f4:d0:50:9f:13:56:9c:a7 (RSA)
    |_256 05:3a:1c:25:35:0a:ed:2f:cd:87:1c:f6:fe:69:e4:f6 (ECDSA)
    31046/tcp open  echo
    31518/tcp open  msdtc   Microsoft Distributed Transaction Coordinator
    (error)
    31691/tcp open  echo
    31790/tcp open  msdtc   Microsoft Distributed Transaction Coordinator
    (error)
    31960/tcp open  echo
    1 service unrecognized despite returning data. If you know the
    service/version, please submit the following fingerprint at
    http://www.insecure.org/cgi-bin/servicefp-submit.cgi :
    SF-Port31000-TCP:V=6.40%I=7%D=1/17%Time=569BA402%P=x86_64-pc-linux-gnu%r(N
    SF:ULL,2B,"SSH-2\.0-OpenSSH_6\.6\.1p1\x20Ubuntu-2ubuntu2\.3\r\n");
    Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

    Service detection performed. Please report any incorrect results at
    http://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 41.47 seconds

So.... nothing new since the netstat. Well, what happens if we just
connect to some of the ports and talk to them? Most of them just
respond with the same stuff we have given to then. But 30001 speaks
SSL. So we can try to connect with a different client.
I feel we are on to something.

    bandit15@melinda:/tmp/graar$  openssl s_client -connect localhost:30001       
    CONNECTED(00000003)
    depth=0 CN = li190-250.members.linode.com
    verify error:num=18:self signed certificate
    verify return:1
    depth=0 CN = li190-250.members.linode.com
    verify return:1
    ---
    Certificate chain
     0 s:/CN=li190-250.members.linode.com
       i:/CN=li190-250.members.linode.com
    ---
    Server certificate
    -----BEGIN CERTIFICATE-----
    MIIC3jCCAcagAwIBAgIJAI5QiWZw4YHbMA0GCSqGSIb3DQEBCwUAMCcxJTAjBgNV
    BAMTHGxpMTkwLTI1MC5tZW1iZXJzLmxpbm9kZS5jb20wHhcNMTQxMTE0MTAyODA0
    WhcNMjQxMTExMTAyODA0WjAnMSUwIwYDVQQDExxsaTE5MC0yNTAubWVtYmVycy5s
    aW5vZGUuY29tMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAsKmy9o5z
    WU+1EH7Z3bB5TGQA+16zXDcEJy6tZWZ8CDrRyQXiahendp45BWUc/ZuLDo0+B3Wt
    ZXjofmLw/F4fmR+8X1s1fQZX2dFt920qEm7LxqzWd0c7FdHiBwwRrwhkk+3cQpOB
    TTGdLWEgpdmwwNZDTUdsDLzjDczPnju6T6p6ArTECztPbmTjfY4QIRtC6capL1Z+
    yPJSQVAuAMEX1wTDWTGdm0VV7oW4F5cGZutf6QAP51jdhSyZuGilIPHbnj0l6Qc7
    a7+OtEsEGi31aJ8KpRf7LNZ7DXCuoB3Hf75Pd6VjDgoOIagcH0NYqa75gEjBkGzs
    ktLWykT7ag7fKwIDAQABow0wCzAJBgNVHRMEAjAAMA0GCSqGSIb3DQEBCwUAA4IB
    AQCaZdUNAj8WDEKWdoU3LNXUBJlTJwiWBrh550PbHSQORcCz2K0kiMei1A4ojK2N
    dMHFGAqAeUEaxtz92p2BoFpZasAtdSa3u63tBckFhfUolIS1TC7Cj51y19ysTeep
    fGPFpuPCVqVPsruei8Z/iqn3bFIhQQdmumeePZQdPMwZSWHNVYC5XODd7PvNDrDu
    5MZJjkz4+6LbwwAvyew62meFN2QEsYbK2Brtbhze+IjE27FGWlSw4K3jlwa409MD
    MTf4JU41ELaYY8G/LSNDJsBVhhkHzvXR9iCbXxNz3IL0dQDNj7h4LKhBy0q7hvqg
    kDzwlmBO4WKSmCAuky44cXmd
    -----END CERTIFICATE-----
    subject=/CN=li190-250.members.linode.com
    issuer=/CN=li190-250.members.linode.com
    ---
    No client certificate CA names sent
    ---
    SSL handshake has read 1714 bytes and written 637 bytes
    ---
    New, TLSv1/SSLv3, Cipher is DHE-RSA-AES256-SHA
    Server public key is 2048 bit
    Secure Renegotiation IS supported
    Compression: NONE
    Expansion: NONE
    SSL-Session:
        Protocol  : SSLv3
        Cipher    : DHE-RSA-AES256-SHA
        Session-ID: F5DE5629DD4FEEAF08302227F2CD97FAF0C435B9603C056C1E33E978E9BBD912
        Session-ID-ctx: 
        Master-Key: 2B6BBA6209CE1655D8F0BC3A123C7FE35217AE91E6C48CE7701169179BAF18ED4EF895BAE179F5A96FD0D20AC184F8F0
        Key-Arg   : None
        PSK identity: None
        PSK identity hint: None
        SRP username: None
        Start Time: 1453043227
        Timeout   : 300 (sec)
        Verify return code: 18 (self signed certificate)
    ---


That is the SSL-handshake which we use to connect.

    <current password>
    HEARTBEATING
    read R BLOCK
    read:errno=0

Why doesn't it give us the password? In fact, i have no idea. What works is if
we give it the argument -quiet which is neither documented in s_client nor in
openssl. Damn! I hate openssl. I have only found this solution through googling.

    bandit15@melinda:/tmp/graar$ cat /etc/bandit_pass/bandit15 | openssl s_client -connect localhost:30001 -quiet
    depth=0 CN = li190-250.members.linode.com
    verify error:num=18:self signed certificate
    verify return:1
    depth=0 CN = li190-250.members.linode.com
    verify return:1
    Correct!
    <next password>
    
    read:errno=0

Got it! Thanks.


## Conclusion
Well, hacking is fun. [Overthewire](http://overthewire.org) is a great site. Cya next time.
