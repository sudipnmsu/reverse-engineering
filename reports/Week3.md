# Week 3 - Basic Dynamic Analysis 

After being familiar with some tools for static analysis and learning some static analysis techniques, now it’s time to execute the malware and see what they do! We will be introduced with basic dynamic analysis this week and run/execute the malware file in a safe environment to understand their behavior. 

## Environment Setup

A short summary of how to prepare the environment before starting with basic analysis:

- **Ubuntu** VM: Let’s set up a Ubuntu VM using VirtualBox and install `net-tools` and `inetsim`. From the VM toolbar, do: `Devices->Networking->Change "NAT" to "Internal Network"`. Add DHCP server using:

`vboxmanage dhcpserver add --enable --network "YOUR_INTERNAL_NETWORK_NAME_HERE" --upperip 10.0.0.30 --lowerip 10.0.0.20 --ip 10.0.0.3 --netmask 255.255.255.0`

Reboot the VM and see the ip address using `ifconfig` command. Finally change ip addresses from line 69 and 207 from the file on this path `/etc/inetsim/inetsim.conf` and restart `inetsim` using `sudo service inetsim restart`.

- **Windows XP VM: ** The malwares seems to be crashing on a previously installed Windows 10 VM, so we will set up a [Windows XP] (https://download.cnet.com/Windows-XP-Mode/3001-18513_4-77683344.html) VM. After extracting with 7z, rename `VirtualXPVHD` to `VirtualXP.VHD` (or `VirtualXP.vdi` for linux host). Next, install 32 bit versions of `strings`, `PEview`, `dependency-walker`, `procmon`, `winshark`, `regshot` etc.

We can connect to the fake internet that we configured on our other linux VM. Obtain the ip address of this Windows VM using `ipconfig` and change ip (windows), gateway (linux), DNS (linux) etc from `Control Panel > Network Connections > Properties > Internet Protocol (TCP/IP) > Properties`. If everything goes well, we will be able to see the `inetsim` default homepage from the `Internet Explorer` browser.

# Lab 3-1

## Executive Summary

 

## Indicators of Compromise



## Mitigations

 

## Evidence

We start our experiment with static analysis using `strings`. Most of the text appear to be undecipherable, there are some strings in readable form:

```
ExitProcess
kernal32.dll
StubPath
SOFTWARE\Classes\http\shell\open\commandV Software\Microsoft\Active Setup\Installed Components\
test
www.practicalmalwareanalysis.com
admin
VideoDriver
WinVMX32-
vmx32to64.exe
SOFTWARE\Microsoft\Windows\CurrentVersion\Run SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders AppData
```

Using `PEview`, we see that the only import is `ExitProcess` and `kernal32.dll` is being used. There might be other imports as well, but we cannot say for sure as the strings are not in recognisable format. It’s also possible that the file is packed, but at least we know it’s not packed with `upx`, since `upx` cannot unpack this file. 


# Lab 3-2

## Executive Summary



## Indicators of Compromise



## Mitigations



## Evidence

The basic `strings` command produces a much bigger output than we usually see. For a better organized report, we open the `.dll` file using `PEview`. We see some of the imported functions `OpenService`, `DeleteService`, `CreateService` which probably being used to manipulating services and some functions `RegCreateKey`, `RegSetValueEx`, `RegOpenKeyEx` for manipulating registry. The `HttpSendRequestA` `InternetOpenA` etc functions are a possible indicator that the file has networking capabilities.

# Lab 3-3

## Executive Summary



## Indicators of Compromise



## Mitigations



## Evidence

For this malware, `string` analysis shows mostly unrecognizable strings and character ‘A’. The **Import Address Table** section in `PEview` lists down a bulk of imports where we see quite a few functions related to ‘Resource’, ‘Process’ and ‘File’ access. We still don’t know much about this malware from these imports and we are going to use dynamic analysis to understand more.




