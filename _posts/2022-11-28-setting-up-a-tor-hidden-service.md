---
layout: post
category: Lesson
tags : [programming, hacking]
---


In this post, I describe how to setup a tor hidden service.
The information can be found scattered among many manpages and
websites. So i tried to collect all of that in one place, in case I
have to do it again.

## Idea
So the other day i got that raspi and had no idea what to do with it.
I decided to set up a tor proxy for my network together with a tor
hidden service.

The tor proxy is in my local network and listens port 9100.
I just have to configure a browser extension such as FoxyProxy to use
it, and can buy cheap flight tickets without being tracked.
Other applications (irc, ssh, ...) that support a SOCKS-Proxy can be tunneled as well.
Note that this is not optimal if you really care about privacy when
browsing the web, as information can be leaked using, e.g., the client
headers.
In this case you have to use the official tor browser bundle. However,
I wanted to have a trade-off between usability and privacy.

The use case of the hidden service is to share files with my friends.
I have the ability to set up my own web server, but I do not want to
pay for a server or a DynDNS entry. Additionally, I do not want to be
watched by DropBox, Google Drive or whatever the current capitalist
overlord is named.
The web should be for the people not for big corporations.

As tor is an overlay network, you do not even need to forward the
ports on your router. Theoretically, you could set up a hidden service
in the guest wifi of a McDonalds branch. (I am not a lawyer. Check
your local laws if this is legal. Probably it is not.)

## Tor Setup
Installing Tor can be done with the package manager on most (or even all) Linux distros.
For Debian based distros, we need to execute `sudo apt-get install tor`.

Next, we need to edit the file `/etc/tor/torrc` to activate the service.
We can uncomment the following lines or simply add them at the end of the file.

    SocksPort 192.168.0.100:9100 # Bind to this address:port. This is the ip of the Pi.
    SocksPolicy accept 192.168.0.0/24
    Log notice file /var/log/tor/notices.log
    RunAsDaemon 1
    DataDirectory /var/lib/tor

This configures the Pi to listen on port 9100 for connection from my network which are then tunneled over Tor.
It configures a log file and a directory where Tor can keep temporary data.

## Monitoring Tor
When we have tor running and can connect from another host, it is time to start monitoring the service.
For this purpose, there is the tool `nyx` which can be installed using the package manager.
Nyx provides a text user interface for various statistics regarding Tor traffic.
In order to use nyx, we need to enable the control port of the tor server to allow nyx to control tor.

First of all, we need to generate the hash of a password which we will use to authenticate to the control port of Tor.
In the example below, i am using the password `aa`. This is only for demonstration purposes.
My real password is a lot more complex. If you are following along at home, please pick a more secure password.

    pi@raspberrypi:/etc/tor $ tor --hash-password aa
    16:A2B35E5E960E017A60E2F9F5A241E6D0330E2E2D65DD3B7AD90A134C60

After we have generated the hash, we need to edit `/etc/tor/torrc` as follows.

    ControlPort 9051
    HashedControlPassword 16:A2B35E5E960E017A60E2F9F5A241E6D0330E2E2D65DD3B7AD90A134C60

This simply opens the port 9051 for control and adds the authentication information.
When we start nyx, we are asked for the password.
This allows nyx to monitor the tor service via its control port.

## Setup of the Hidden Service
Next, we set up the hidden service.
First of all, we install a web server such as nginx.
We restrict nginx to localhost, such that it is only available using tor and not via the normal internet.
This is for hardening reasons, as we do not want to use the server over the internet anyway.
Note that we do not need TLS certificates, as the connection is encrypted and authenticated anyway due to usage of tor.

As we are using nginx, we can restrict the service to localhost by adding
`listen 127.0.0.1:80 default_server;` in the server directive in
`/etc/nginx/sites-available/default`.

Next we need to tell tor about our hidden service by editing `/etc/tor/torrc` as follows:

    HiddenServiceDir /var/lib/tor/hidden_service/
    HiddenServicePort 80 127.0.0.1:80

This instructs tor to store the data concerning the hidden service at `/var/lib/tor/hidden_service`.
It does not include the content of the website, as that is served by nginx.
Rather, Tor stores the key pair and the host name for the service there.
In particular, the host name is in the file `/var/lib/tor/hidden_service/hostname`.

## Host name Creation

By default the host name of the hidden service is ugly and may look like the following:
`545ek374745gyewvnm3eeu7dr7oangha35v7nldz5sntsqy2x4zj75qd.onion`.
This is not easy to remember and looks simply ugly.
As far as i know, the host name is the base32 encoding of the first 80 characters of the hash of the public key or something similar.
As the hash function works as a one-way function, a beautiful host name is not easy to choose directly.
The naive approach is to guess secret keys, compute the corresponding host names and stop if we have found a nice one.
Fortunately someone else has already written code for that.
There is [Shallow](https://github.com/katmagic/Shallot) and [Eschalot](https://github.com/ReclaimYourPrivacy/eschalot) which unfortunately only work with RSA keys.
For ed25519 keys, we need to use [mk224o](https://github.com/cathugger/mkp224o).
This allows us to specify a prefix and returns the data directory with the corresponding keys for a host name starting with that prefix.
Just make sure to choose a l33t prefix such as "silkroad".

Example usage is as follows:

    pi@raspberrypi:~/mkp224o-master $ ./mkp224o -S 30 -d tmp silkroad

The argument to `-S` prints statistics every specified amount of seconds.
The argument to `-d` is the name of the output directory.
If the tool terminates, just copy the output directory to `/var/lib/tor/hidden_service/` and you are done.
