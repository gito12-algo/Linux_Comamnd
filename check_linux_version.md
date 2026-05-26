# How to Check Linux Version (for the Operating System, and the Kernel)
It can be hard to know exactly what Linux distribution is running here. How is the operating system called? Ubuntu, Debian, or Red Hat? And if it's Ubuntu, what version of Ubuntu?

Just scroll down to see quick answers about:

How to find out the name of the Linux operating system. Or, to be more accurate, the operating system based on the Linux kernel. Is it Ubuntu, CentOS, Debian, or something else?
How to find out the version of the Linux operating system. Is it Ubuntu 22.04, or 24.04? CentOS 8, or CentOS 9?
How to find out the version of the Linux kernel powering the operating system. Is it Linux kernel 6.12.11, or something older / newer?
But wait, "Isn't Linux an operating system?"

No, Linux is the kernel, the center, or the heart. And operating systems are built around that kernel. Just like a car is built around an engine.
### Check Linux Version with One Command
There is a command to get ALL of this information: The name of the Linux operating system, the operating system version, and the kernel version:
```
hostnamectl status
```
### Check Linux Operating System Version, Name, and Codename
```
cat /etc/os-release
```
### Check Linux Kernel Version
```
uname -r
```
### If uname is not available, there's another command we can use:
```
cat /proc/version
```
