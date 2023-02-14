# Week 3 - Basic Dynamic Analysis 

After being familiar with some tools for static analysis and learning some static analysis techniques, now it’s time to execute the malware and see what they do! We will be introduced with basic dynamic analysis this week and run/execute the malware file in a safe environment to understand their behavior. 

## Environment Setup

A short summary of how to prepare the environment before starting with basic analysis:

- **Ubuntu** VM: Let’s set up a Ubuntu VM using VirtualBox and install `net-tools` and `inetsim`. From the VM toolbar, do: `Devices->Networking->Change "NAT" to "Internal Network"`. Add DHCP server using:

`vboxmanage dhcpserver add --enable --network "YOUR_INTERNAL_NETWORK_NAME_HERE" --upperip 10.0.0.30 --lowerip 10.0.0.20 --ip 10.0.0.3 --netmask 255.255.255.0`

Rebook the VM and see the ip address using `ifconfig` command. Finally change ip addresses from line 69 and 207 from the file on this path `/etc/inetsim/inetsim.conf` and restart `inetsim` using `sudo service inetsim restart`.

- **Windows XP VM:** The malwares seems to be crashing on a previously installed Windows 10 VM, so we will set up a [Windows XP](https://download.cnet.com/Windows-XP-Mode/3001-18513_4-77683344.html) VM. After extracting with 7z, rename `VirtualXPVHD` to `VirtualXP.VHD` (or `VirtualXP.vdi` for linux host). Next, install 32 bit versions of `strings`, `PEview`, `dependency-walker`, `procmon`, `winshark`, `regshot` etc. 


We can connect to the fake internet that we configured on our other linux VM. Obtain the ip address of this Windows VM using `ipconfig` and change ip (windows), gateway (linux), DNS (linux) etc from `Control Panel > Network Connections > Properties > Internet Protocol (TCP/IP) > Properties`. If everything goes well, we will be able to see the `inetsim` default homepage from the `Internet Explorer` browser.

# Lab 3-1

## Executive Summary

 

## Indicators of Compromise



## Mitigations

 

## Evidence



# Lab 3-2

## Executive Summary



## Indicators of Compromise



## Mitigations



## Evidence


# Lab 3-3

## Executive Summary



## Indicators of Compromise



## Mitigations



## Evidence


