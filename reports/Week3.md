# Week 3 - Basic Dynamic Analysis 

After being familiar with some tools for static analysis and learning some static analysis techniques, now it’s time to execute the malware and see what they do! We will be introduced with basic dynamic analysis this week and run/execute the malware file in a safe environment to understand their behavior. 

## Environment Setup

A short summary of how to prepare the environment before starting with basic analysis:

- **Ubuntu** VM: Let’s set up a Ubuntu VM using VirtualBox and install `net-tools` and `inetsim`. From the VM toolbar, do: `Devices > Networking > Change "NAT" to "Internal Network"`. Add DHCP server using:

`vboxmanage dhcpserver add --enable --network "YOUR_INTERNAL_NETWORK_NAME_HERE" --upperip 10.0.0.30 --lowerip 10.0.0.20 --ip 10.0.0.3 --netmask 255.255.255.0`

Reboot the VM and see the ip address using `ifconfig` command. Finally change ip addresses from line 69 and 207 from the file on this path `/etc/inetsim/inetsim.conf` and restart `inetsim` using `sudo service inetsim restart`.

- **Windows XP VM:** The malwares seems to be crashing on a previously installed Windows 10 VM, so we will set up a [Windows XP] (https://download.cnet.com/Windows-XP-Mode/3001-18513_4-77683344.html) VM. After extracting with 7z, rename `VirtualXPVHD` to `VirtualXP.VHD` (or `VirtualXP.vdi` for linux host). Next, install 32 bit versions of `strings`, `PEview`, `dependency-walker`, `procmon`, `winshark`, `regshot` etc.

We can connect to the fake internet that we configured on our other linux VM. Obtain the ip address of this Windows VM using `ipconfig` and change ip (windows), gateway (linux), DNS (linux) etc from `Control Panel > Network Connections > Properties > Internet Protocol (TCP/IP) > Properties`. If everything goes well, we will be able to see the `inetsim` default homepage from the `Internet Explorer` browser.

# Lab 3-1

## Executive Summary

This malware creates a version of itself in the host machine and seems to have the capacity of accessing the internet and sending random (unknown) data. Using dynamic analysis, we find that it makes requests to an external URL which we found during static analysis. 

## Indicators of Compromise

- Both Lab03-01.exe and `vmx32to64.exe` have the same MD5: `d537acb8f56a1ce206bc35cf8ff959c0`. 
- URL from static analysis: `www.practicalmalwareanalysis.com`.
- `.dll` files (`wshtcpip.dll`, `ws2_32.dll`) using Socket.

## Mitigations

- Look for: `C:\Windows\system32\vmx32to64.exe` and delete it along with the original malware.
- Block network access for `www.practicalmalwareanalysis.com`
- Scan for `wshtcpip.dll`, `ws2_32.dll` files and delete.

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

Using `PEview`, we see that the only import is `kernal32.dll` used by `ExitProcess`. There might be other imports as well, but we cannot say for sure as the strings are not in recognisable format. It’s also possible that the file is packed, but at least we know it’s not packed with `upx`, since `upx` cannot unpack this file. 

Before running the malware, we start `procmon` and `process-explorer`. In the `procmon` analysis, we find a `vmx32to64.exe` having the same MD5 hash (confirmed by VirusTotal). The `process-explorer` output (View > Lower Pane View > Handles) shows that the malware has created a mutant file `WinVMX32`. It also shows (View > Lower Pane View > DLLS) a few `Socket` based `.dll` files which confirms the internet access capability of this malware. We filter with ‘practicalmalwareanalysis’ in `Wireshark` and see that the URL makes requests following the DNS protocol.

# Lab 3-2

## Executive Summary

This `.dll` file runs as a service with the association of `svchost.exe` process and communicates with the outside world using an URL `www.practicalmalwareanalysis.com`. We do not have any evidence that it sends any data from the host, but we see it performing GET operation.

## Indicators of Compromise

- Communicates with `www.practicalmalwareanalysis.com`.
- MD5: `84882c9d43e23d63b82004fae74ebb61`
- Description text that indicates it collects and reports any change of information over the network.
- See if any of the `svchost.exe` is matching with the Display Name or Description of this malware.

## Mitigations

- Look for network signatures to detect the pattern for this malware and block it.
- Scan using the hash and delete it.


## Evidence

The basic `strings` command produces a much bigger output than we usually see. For a better organized report, we open the `.dll` file using `PEview`. We see some of the imported functions `OpenService`, `DeleteService`, `CreateService` which probably being used to manipulating services and some functions `RegCreateKey`, `RegSetValueEx`, `RegOpenKeyEx` for manipulating registry. The `HttpSendRequestA` `InternetOpenA` etc functions are a possible indicator that the file has networking capabilities.

From the export address table in `PEview`, we assume that we need to install the `.dll` file to run it. We can try installing the malware using this command: `C:\rundll32.exe \path-to-file\Lab03-02.dll, installA`. We need to take snapshots of the registry before and after the installation process and compare the output. The comparison indicates a service `IPRIP` created by the malware. As we know from Lab-01, `.dll` file typically needs an associate `.exe` file to run, we see a `.exe` file named as `svchost.exe` under ‘Values Added’ section. To run this file properly, we launch it as a service using `C:\net start iprip`. 

There are several `svchost.exe` listed on Process Explorer, we can search for `Lab03-02.dll` to look for the `svchost.exe` that is running the malware and note its PID (1080 for this experiment). In `procmon`, we can use this PID to locate the malware. The malware installs itself with the display name of `Intranet Network Awareness (INA+)` and description indicates that it collects network information and notices information changes. There are socket `.dll` evidences (`wshtcpip.dll`, `ws2_32.dll`) like the previous lab to prove that it communicates with the network, `www.practicalmalwareanalysis.com` to be specific. We see that it performs a GET request on port 80 using NetCat - the parts that we can use as network signatures are GET request over serve.html and `User-Agent: YOUR_DEVICE_NAME Windows XP 6.11`. [^1]

[^1]: The `NetCat` evidence is taken from the book’s solution. I couldn’t find a working version for XP and test it by myself.


# Lab 3-3

## Executive Summary



## Indicators of Compromise



## Mitigations



## Evidence

For this malware, `string` analysis shows mostly unrecognizable strings and character ‘A’. The **Import Address Table** section in `PEview` lists down a bulk of imports where we see quite a few functions related to ‘Resource’, ‘Process’ and ‘File’ access. We still don’t know much about this malware from these imports and we are going to use dynamic analysis to understand more.




