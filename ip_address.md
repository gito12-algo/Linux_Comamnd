# Linux: Get IP Address
### Linux Command to Display IP Addresses
No matter if we're running Ubuntu, Debian, something from the Red Hat family (or any other popular Linux distribution) there should be a command called ip that we can use.

To see all IP addresses of a Linux machine, we can run a command like this:
``
ip -c address
```
To install curl on Ubuntu / Debian systems, or anything derived from this family (that uses .deb software packages), we can run:
```
sudo apt install curl
```
Then to get the public IP address of a Linux machine, we can run any of these commands:
```
curl icanhazip.com
curl checkip.amazonaws.com
curl ifconfig.me
```
