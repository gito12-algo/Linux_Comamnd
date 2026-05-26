# How to check Disk Space on Linux

We still call it "disk space", a remnant of the era when almost everything was stored on hard disks. Nowadays, since data can be stored on SSDs, or other devices, a more generic term like "storage space" is used. But to keep in line with what people are searching for, we'll call it "disk space" in this blog.

We have multiple ways of checking what disk space is available on Linux systems, and how it's used. Let's go through the most usual methods.
### Check Total Disk Sizes
To check the sizes of our disks, we can use the lsblk command:
```
lsblk
```

Whenever we write a file, or a directory, it's saved in a filesystem. Which means that only the filesystem will know how much space on our disks is actively used, and how much is free.
### Check Disk Space on Filesystems
There is a Linux command, called df, that will show us the following things:

- Total disk space on each filesystem.
- Used disk space on each filesystem.
- Free disk space available on each filesystem.
```
df -hx tmpfs
```
### But if we add the -h option, which stands for "human readable", we get this:
```
df -h
```
### How to Find the Largest Directories on a Linux System
```
sudo du -h --max-depth=5 / | sort -h
```
### Also, it's worth mentioning that we can move the starting point of du from / to something else. So after we scanned with a command like:
```
sudo du -h --max-depth=5 / | sort -h
```
### Visually Inspect Directory Sizes with the "ncdu" utility
Another interesting utility to inspect directory sizes is the ncdu utility. Which can be installed with:
```
sudo apt install ncdu
```
### And then we can run ncdu, followed by the directory we want to start scanning from. Which is usually the root directory /:
```
ncdu /
```
