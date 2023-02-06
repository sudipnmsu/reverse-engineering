# Week 2 - Simple Static Analysis (Part 2)

We will continue static analysis with two new malware this week. 

## Tools:
- `strings`: https://learn.microsoft.com/en-us/sysinternals/downloads/strings
- `PEview`: http://wjradburn.com/software/
- `dependency walker`: https://www.dependencywalker.com/
- `upx`: https://upx.github.io/

# Lab 1-3

## Executive Summary

This file is detected as malware by 60 out of 70 companies according to VirusTotal. We also get the indication that the file is packed, but we currently do not have the tools to unpack the file and analyze the content.  

## Indicators of Compromise

- VirusTotal report
- File is packed

We do not have any better indicator at this moment.

## Mitigations

Since the file is most obviously a virus according to VirusTotal, we should delete the file. 

## Evidence

The `strings` analysis does not provide much information about this file. To investigate further if the file is packed or not, we open it using `PEview`. The `PEview` does not give us much information as it did for the previous lab. We try to unpack the file using `upx`, but `upx` cannot unpack this file since it was packed using another packer. We see that VirusTotal mentions FSG packer but we don’t know how to use that tool yet.

# Lab 1-4

## Executive Summary

This program is not packed and `strings` analysis indicates that it may download another virus using a URL which contains .exe file extension. There is a possibility of accessing and manipulating system files by the program which we assume by the function name it contains.

## Indicators of Compromise

- URL that contains a .exe extension.
- Some function names that probably are  being used for accessing system files.

## Mitigations

- Inspect the URL in a virtual machine.
- Looking for any suspicious network access and possibly try to block it.

## Evidence

We do not see any indication of the file being packed using `PEview`. The `strings` shows a URL `www.malwareanalysisbok.com/updater.exe` which probably downloads another `exe` file that can possibly be a malware. There are some function names, such as `LoadResource`, `FindResource`, along with `KERNEL32.DLL` give us indication that the program may access data from the resource section and probably try to use it for something. It also probably tries to write in the system directory using the `GetWindowsDirectory` function. 