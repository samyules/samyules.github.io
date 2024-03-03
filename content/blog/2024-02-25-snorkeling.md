---
title: "Snorkeling"
description: Description of my public ip over tailnet setup.
date: 2024-02-25
tags: [networking, snorkel, cg-nat, public-ip, tailscale]
---
I am now a T-mobile Home Internet customer. It uses 4G LTE and 5G cellular networks to provide internet connectivity. They provide you with a cellular modem/router with a wifi hotspot and a couple of ethernet ports. I figured out how to disable the wifi and connected it to the WAN port of my UDM Pro. Easy-peasy.

There are some trade offs. There is some variability in the signal quality, which translates to extra latency some of the time. The cellular networks also use CG-NAT and **T-mobile does not provide a public IP address.**

I could get around this by setting up some *Cloudflare tunnels*. They are easy and work great. The main problem is that I want to control my own SSL certificates. Cloudflare creates a wildcard certificate for subdomains (*.mydomain.io), but only root level subdomains. Inside my LAN I use sub-subdomains. For example, *service.computername*.mydomain.io. For publicly exposed services I prefer using the same URL inside my LAN and over WAN, and Cloudflare tunnels are not able to give me that.

My LAN is now diving beneath the depths CG-NAT. I need a snorkel.

```txt
    public ip (vps)

     |  s  |
/\/\/|  n  |\/\/\/\/\/\/\/\/\/\/\/\/\/\
     |  o  |  Deep Waters of CG-NAT
     |  r  |
     |  k  |
     |  e  |
     |  l  |
     \     \________
      \  tailscale --> DMZ --> LAN
       \____________

```

So basically, I’ve got a VPS with a public IP. It connects to my DMZ proxy server through my Tailscale tailnet. The VPS forwards any traffic it receives on ports 80 and 443 to the proxy server. **¡Viola! A snorkel!**

## Forwarding Packets
My initial thought was to turn my VPS into a router. I tried using iptables to forward ports, but I’m not very good at writing rules and had a few problems. I couldn’t figure out how to forward packets through tailscale. 🤔 After some more searching around the interwebs I found an old program called *[rinetd](https://github.com/samhocevar/rinetd)*.

### rinetd setup
*rinetd* silently forwards tcp and udp traffic. It is found in most linux package managers. I’m using an Ubuntu server, so I installed it with an easy `sudo apt install rinetd`. The configuration file is found in `/etc/rinetd.conf`. The main section looks like this:

```bash
# bindaddress bindport connectaddress connectport
137.184.89.83 80 100.86.67.77 80
137.184.89.83 443 100.86.67.77 443
```
The `bindaddress` is the public ip of the VPS, and the `connectaddress` is the tailnet address of the proxy server. Easy. After updating the configuration, run a quick `systemctl start rinetd` and you are set. No need to mess with forwarding rules. It just works.

Time to make more coffee.
