+++
date = "2016-09-18T20:54:13-07:00"
title = "Service discovery with mDNS, Raspberry Pi and Go"

+++
Today, yet again, I re-formatted by Raspberry Pi. It has become a sort of a ritual for me to format my Raspberry Pi and try out something new on it. This time around however, as I had moved to a new apartment, logging into my Raspberry Pi meant that I had to change my `/etc/hosts` mapping.

Now, `/etc/hosts` mapping had served my well but I had grown tired of updating this file, every time moved to a new place, despite of the static IPs that I assign. After digging around on the Internet I figured out that setting up an Avahi like zeroconf tool would probably be the best solution for my problem.

While I was thinking of doing this, it occurred to me that I probably didn’t need all of the nice things that Avahi brings. All I needed was way to SSH into my raspberry pi without knowing its IP address. And that’s when I ended up doing something like [this xkcd cartoon](https://xkcd.com/1319/) .

I started out with [Hashicorp’s mDNS library in Go](https://github.com/hashicorp/mdns). Soon, I felt that I might run off multiple services in my Raspberry pi, and as the library did not seem to support this, I decided to create a fork and add the functionality to it. Once this was done, I cross compiled the binary on my Mac and scp’ed the binary to my Raspberry pi.

After spending a few more minutes fighting with `systemd` I finally managed to log in to my Raspberry pi without knowing its IP.

As a bonus, I also figured out that my housemate’s Nexus device performs periodic mDNS lookups for Chromcast devices on the network. :)

_PS: You can find the Github repo for my mDNS service, [here](https://github.com/aishraj/mdns)_
