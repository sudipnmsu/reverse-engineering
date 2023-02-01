# Week 1 - Simple Static Analysis

This week we are going to learn about Static Analysis techniques using different tools such as strings, PEview and dependency-walker. We will also explore the VirusTotal tool (by Google) to see the analysis results of malware, scanned by lots of existing antivirus tools. We will not actually run any program at this point, the goal of this week is to extract useful information using static analysis techniques.

# Lab 1-1

## Executive Summary

According to the VirusTotal report, 60% of the antivirus tools mark the dll file as a virus and the percentage goes up to 75% in case of the exe file. The `.dll` file contains an IP address and possibly sends something to the outside source; as the `.dll` file cannot run by itself, it should be associated with the `.exe` file. They also seem to hide a `kerne1.dll` file in the `system32` directory, though it’s unknown what that file does.

## Indicators of Compromise
- Monitor network to see if that particular IP (127.26.152.13) is sending anything concerning.
- Check if the hashes are already identified as a virus by another program. 

MD5 Hash (dll): `290934c61de9176ad682ffdd65f0a669`,
MD5 Hash (exe): `bb7425b82141a1c0f7d60e5106676bb1`

- A good way to look for the first compilation date. A new virus tends to be more dangerous than an old one.

## Mitigations
- Take necessary steps to disable (block/drop packages) the IP address. 
- Delete files matching with the hash(es).
- Look for `kerne1.dll` and delete it from `system32`.

## Evidence
With the help of  `strings`, we see the presence of an IP address in the `.dll` file. Though the IP is a local address here, it may not be the case in an actual virus. We also see `kerne1.dll` which is similar to `kernel.dll` - this is an indication of hiding something suspicious using an almost similar name to one of the system files. 

If we check the compilation time using `PEview`, the files were compiled at the same time. That makes it almost certain that the files are created by the same person and are helpers of each other.


# Lab 1-2

## Executive Summary

This file was packed using `upx`, which makes it suspicious because the malware creators often use packing or obfuscation techniques to avoid detection. After unpacking the file, we see an URL and some function names indicating that the program may be trying to open that specific URL and establish some sort of communication.

## Indicators of Compromise
- From basic `strings` analysis: `UPX0, UPX1, UPX2` are the indicators of being a packed file.
- `http://www.malwareanalysisbook.com` URL and functions `InternetOpenUrlA` `InternetOpenA`.

## Mitigations
- Delete the file.
- Monitor network for tracing the outgoing packages for the URL. 

## Evidence
As we do the basic analysis with `strings`, we find the `UPX0, UPX1, UPX2` strings showing that the file is packed by `upx`. We get further evidence by opening the file with PEview and confirm the presence of `SECTION UPX0/UPX1/UPX2`.

Now after we unpack the file with `upx`, the strings are in a more readable shape. We see some interesting function names such as `InternetOpenUrlA` `InternetOpenA` and an URL http://www.malwareanalysisbook.com, but it is not clear if the URL is trying to transfer any or what kind of data. A possible approach could be creating a virtual machine and investigate further what the URL does.






