# CS579 Reverse Engineering at NMSU

## Summary

Welcome to the world of Reverse Engineering! This repo will contain the reports of the projects we will be doing throughout the semester. We will be looking into different examples from the book "Practical Malware Analysis" to understand different malware. 

## System Setup

As it is very dangerous to analyze malware or viruses in any machine, we will be setting up virtual machine to protect the host computer from any potential damage. We will choose Linux OS (Ubuntu 22.04) and install VirtualBox which will provide us a virtual environment to run our victim machine.

To install VirtualBox in Ubuntu, we can follow the steps in this link:

https://tecadmin.net/how-to-install-virtualbox-on-ubuntu-22-04/

The next step is setting up Windows in the virtual environment. Download Windows iso file can be downloaded from here:

https://www.microsoft.com/en-us/software-download/windows10ISO

Launch VirtualBox and start installing the Windows OS by clicking the ‘New’ option from the menu. During this process, choosing RAM size can be trickier. Our goal is to allocate more RAM to the virtual machine to ensure a smoother experience, but this could be the most obvious reason for the virtual OS crashing at any moment. Generally ¼ of the total RAM size should work fine for the virtual OS, but we may need to try with a lower size in case the system is not behaving as expected.

After creating a new virtual machine, click ‘Start’ to install/launch. We will always make sure to disable any network interface, such as the internet, to keep the malware analysis in a confined space and keep the host computer safe.

## Frequently Used Tools:

As we progress, we will list down all the tools we are using.

Week-1

- `strings`: https://learn.microsoft.com/en-us/sysinternals/downloads/strings
- `PEview`: http://wjradburn.com/software/
- `dependency walker`: https://www.dependencywalker.com/
- `upx`: https://upx.github.io/

..
