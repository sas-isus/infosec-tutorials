Nmap Tutorial
=============

By: @_clayball


This tutorial will cover the basic usage details of Nmap along with a few use 
cases and advanced scanning techniques. Please review the IPtables Primer if
you're not comfortable opening and closing ports for new or existing chains.


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
as easy as.. (RHEL/CentOS 6 users replace ```dnf``` with ```yum```)

```$ sudo dnf install nmap```

or for Debian related folks..

```$ sudo apt-get install nmap```

Installing Nmap using the package manager for your distro will limit you to
the specific version packaged for your distro's version. The following table
include the version of Nmap with the distro's version as of May, 2017.

TODO: complete the table below

| Distro    | Version | Nmap Version |
| --------- | ------- | ------------ |
| CentoOS   | 6       |
| CentoOS   | 7       |              
| RHEL      | 6       |
| Ubuntu    |         |
| Ubuntu    |         |
| Debian    |         |
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
$ nmap -sS -T3 -p80,443 -sV --version-intensity 3 -iL subnets.csv --open -oX scan-results.xml
```

Before we break down the command above, lets cover some basics.

Lets start with the most basic Nmap scan. Our target will be scanme.nmap.org.
Thank you, Fyodor!




## Resources

TODO:

