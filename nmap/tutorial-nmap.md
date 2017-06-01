Nmap Tutorial
=============

By: @_clayball


This tutorial will cover the basic usage details of Nmap along with a few use 
cases and advanced scanning techniques. You may want to review the IPtables
tutorial if you're not comfortable opening and closing ports for new or
existing chains.

``` bash
          :================:
         /||# nmap -A _   ||
        / ||              ||
       |  ||              ||
        \ ||              ||
          ==================
   ........... /      \.............
   :\        ############            \
   : ---------------------------------
   : |  *   |__________|| ::::::::::  |
   \ |      |          ||   .......   |
     --------------------------------- 8
```

  NMAP IS A POWERFUL TOOL -- USE CAREFULLY AND RESPONSIBLY


## Introduction

"Nmap ("Network Mapper") is a free and open source (license) utility for
network discovery and security auditing. Many systems and network
administrators also find it useful for tasks such as network inventory,
managing service upgrade schedules, and monitoring host or service uptime.
Nmap uses raw IP packets in novel ways to determine what hosts are available
on the network, what services (application name and version) those hosts are
offering, what operating systems (and OS versions) they are running, what
type of packet filters/firewalls are in use, and dozens of other
characteristics. It was designed to rapidly scan large networks, but works
fine against single hosts. Nmap runs on all major computer operating systems,
and official binary packages are available for Linux, Windows, and Mac OS X.
In addition to the classic command-line Nmap executable, the Nmap suite
includes an advanced GUI and results viewer (Zenmap), a flexible data
transfer, redirection, and debugging tool (Ncat), a utility for comparing
scan results (Ndiff), and a packet generation and response analysis tool 
(Nping)."

- https://nmap.org

Nmap's original author is Gordon “Fyodor” Lyon.


## Installation

Most, if not all, Linux distributions package nmap. So, installation should be
as easy as.. 

RHEL based distros

```$ sudo yum install nmap```

Fedora

```$ sudo dnf install nmap```

Debian-based distros

```$ sudo apt install nmap```

Gentoo

```root # emerge --ask net-analyzer/nmap```


Installing Nmap using the package manager for your distro will limit you to
the specific version packaged for your distro's version. The following table
include the version of Nmap with the distro's version as of May, 2017.

TODO: complete the table below

| Distro    | Version | Nmap Version |
| --------- | ------- | ------------ |
| CentoOS   | 6       |
| CentoOS   | 7       |              
| RHEL      | 6       |
| RHEL      | 7       |
| Fedora    | 25      | nmap-7.40-1
| Ubuntu    |         |
| Ubuntu    |         |
| Debian    | 8.8.0   |
| Kali      |         |


For most situations this is totally fine. However, if you'd like to utilize
newer features of Nmap and newer NSE scripts then you should consider
cloning the Nmap repo from Github and compiling from source. This is how we
did it back in the day! and is somewhat of a lost art.

### Compiling Nmap from source

In order to keep a tidy system, I prefer installing certain programs to my
user directory, e.g. /home/USERNAME/local. This requires updating my PATH.

```
$ ./configure --prefix=/home/USERNAME/local
$ make
$ make install
```

Nmap will now be installed in ```/home/USERNAME/local/bin```.

Add the path to your ```~/.bashrc``` file..

```export PATH=$PATH:/home/USERNAME/local/bin```


Confirm Nmap is installed..

```$ which nmap```

You should see..

```~/local/bin/nmap```

Excellent! Now, lets dig into Nmap.


## Getting Started

I use Nmap frequently and have a number of daily cron jobs in place that
perform various tasks. One of those tasks is to identify all machines with
port 80 and 443 open across a number of subnets. This is accomplished using
the following command..

```
$ sudo nmap -sS -T3 -p80,443 -sV --version-intensity 3 -iL subnets.csv --open -oX scan-results.xml
```

Before we break down the command above, lets cover some basics and build upon
them.


### Basic Nmap scan

Lets start with the most basic Nmap scan. Our target will be scanme.nmap.org.
Thank you, Fyodor!

```$ sudo nmap scanme.nmap.org```

Running the default scan produces the following output..

```
Starting Nmap 7.40 ( https://nmap.org ) at 2017-05-31 21:08 EDT
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.083s latency).
Other addresses for scanme.nmap.org (not scanned):
2600:3c01::f03c:91ff:fe18:bb2f

Not shown: 993 closed ports
PORT      STATE    SERVICE
22/tcp    open     ssh
25/tcp    filtered smtp
80/tcp    open     http
554/tcp   open     rtsp
7070/tcp  open     realserver
9929/tcp  open     nping-echo
31337/tcp open     Elite

Nmap done: 1 IP address (1 host up) scanned in 4.17 seconds
```

The simple command ```nmap target``` will scan the most common 1,000 ports for
each protocol on the target host. Nmap default scan includes the following
options..

    -sS (TCP SYN scan)
    -T3 (Set a timing template) 3=normal


### Port States

These details can be reviewed by consulting Nmap's man page.

**The six port states recognized by Nmap**

open
    An application is actively accepting TCP connections, UDP datagrams or
    SCTP associations on this port. Finding these is often the primary goal
    of port scanning. Security-minded people know that each open port is an
    avenue for attack. Attackers and pen-testers want to exploit the open
    ports, while administrators try to close or protect them with firewalls
    without thwarting legitimate users. Open ports are also interesting for
    non-security scans because they show services available for use on the
    network.

closed
    A closed port is accessible (it receives and responds to Nmap probe
    packets), but there is no application listening on it. They can be
    helpful in showing that a host is up on an IP address (host discovery, or
    ping scanning), and as part of OS detection. Because closed ports are
    reachable, it may be worth scanning later in case some open up.
    Administrators may want to consider blocking such ports with a firewall.
    Then they would appear in the filtered state, discussed next.

filtered
    Nmap cannot determine whether the port is open because packet filtering
    prevents its probes from reaching the port. The filtering could be from a
    dedicated firewall device, router rules, or host-based firewall software.
    These ports frustrate attackers because they provide so little
    information. Sometimes they respond with ICMP error messages such as type
    3 code 13 (destination unreachable: communication administratively
    prohibited), but filters that simply drop probes without responding
    are far more common. This forces Nmap to retry several times just in case
    the probe was dropped due to network congestion rather than filtering.
    This slows down the scan dramatically.

unfiltered
    The unfiltered state means that a port is accessible, but Nmap is unable
    to determine whether it is open or closed. Only the ACK scan, which is
    used to map firewall rulesets, classifies ports into this state. Scanning
    unfiltered ports with other scan types such as Window scan, SYN scan, or
    FIN scan, may help resolve whether the port is open.

open|filtered
    Nmap places ports in this state when it is unable to determine whether a
    port is open or filtered. This occurs for scan types in which open ports
    give no response. The lack of response could also mean that a packet
    filter dropped the probe or any response it elicited. So Nmap does not
    know for sure whether the port is open or being filtered. The UDP, IP
    protocol, FIN, NULL, and Xmas scans classify ports this way.

closed|filtered
    This state is used when Nmap is unable to determine whether a port is
    closed or filtered. It is only used for the IP ID idle scan.




## Resources

[Nmap Website](http://nmap.org)


