## How to Terminate, or Kill a Process in Linux (Complete Guide
As long-term computer users, we've certainly had to use something like Windows' Task Manager, at least a few times. An application becomes unresponsive, or slow, or simply misbehaves. And clicking on that "X" button just doesn't work. So we fire up Task Manager, right-click on the "bad app", and "End task" it.

But what's the Linux equivalent to Task Manager? When all we have is an SSH connection to some remote Linux server? And just a command line to play in?

# In this guide, we'll answer questions like these:
- How do I find the PID of the process I want to terminate? Based on the process name.
- How do I find the PID of the process with the highest CPU utilization?
- How do I find and terminate a process that is listening to a certain port?
- How do I gracefully terminate / stop a process on Linux?
- How do I force-kill it if a graceful terminate does not work? (process unresponsive / stuck).
- What if it's a long-running process that should be restarted, how do I terminate, and then re-initialize that process (with systemd).
### Step 1 – Find the PID of the Process
The first thing that we need to find is the so-called PID of that process. Think of it as Process IDentifier. A number that uniquely identifies this process, and only this process. No other process will have the same PID.

### Scenario 1 – Find the PID Based on the Process, or Application Name
Let's say that we noticed our web server is stuck. Just hangs when we try to retrieve a web page. So we suspect something went wrong with our Nginx application. Knowing this name, nginx, here's what we can do.

First, we look for all processes which have nginx in their name (replace nginx in the next command with whatever applies to your situation):
```
pgrep -a nginx

```
The -a option tells the pgrep command to show us additional information. Without it, we'd only see the PID numbers, but nothing else.
### Scenario 2 – Find the PID of the Process with the Highest CPU Usage
In some cases, we won't know what application is actually causing issues. We won't know its name. We might be thinking that:

Something is slowing down this server. But I'm not sure what process is doing that.
What we can do is look at what is intensively using our CPUs. We can do that with the top command:
```
top
```
# Hint: to close the top program, press the "q" key.
Our focus should be on the %CPU column. In this example, we have just two processes with higher-than-normal CPU usage (21.6% and 20.6%). One has PID 207112. And one has PID 26048.

top will periodically refresh this data. So we can get an idea on what is constantly using a lot of CPU power. Since some things might spike up at the top of this table, but then quickly disappear. For example, maybe our server gets a database request. And we might see the "mysql" process quickly spike up for a share of 50% of one CPU core. But if it goes down in a second, then it's probably not what is causing the issue.

Look at what is persistently showing up with a very high amount of %CPU usage. It's relative what counts as "very high". But if it's a program that shouldn't do much, and it's constantly up there with 80-160% CPU usage, or more, for no good reason, then it's probably stuck in a loop. Or misbehaving in some way.
# 
Note: Yes, the CPU column can show usage above 100%. For example, if we have 4 cores on a server and an application is using all of them, at full capacity, we might see usage around 400% in that column.
## Step 2 – Terminate the Process with systemd or the "kill" Command
Most of the long-lived processes on Linux are managed by something called systemd. Or, at least, a part of it does that. Since systemd covers many other responsibilities. Think of this part as a "manager" that makes sure certain applications are started at the right times. And that their processes have a long, healthy life. Also, if anything goes wrong, systemd will try to restart those processes, or take corrective actions (if it can).

Not all Linux distributions use systemd, but most of them do.

How do we check if our process is managed by systemd? Easily enough, we just run the systemctl status command, followed by the PID we found.

In our case, it would be:
```
systemctl status 1954
```

### Scenario 2 – Terminate the Process Using the "kill PID" Command
- Maybe the process is managed by systemd, but the systemctl stop command did not work.
- Or the process is not managed by systemd.
- Or the Linux distribution does not use systemd at all.
In either case, this time we'll go a different route: Interact with that process directly, without the help of systemd.

Let's say the PID of the process we want to stop is 778 this time. So just replace 778 in the next commands with whatever applies to you.

The first step we can try with a misbehaving process:

Method 1 – Try to Terminate the Process Gracefully (SIGTERM)

We can run this command to try to gracefully terminate (stop) a process on Linux:
```
sudo kill -s SIGTERM 778
```
# 
Note: sudo kill 778 would have also worked, and had the same effect. Since, by default, the kill command sends the SIGTERM signal anyway. But I prefer to use the specific options (-s SIGTERM) for commands that could be disruptive. Because, what if the default changes in the future, for some reason? And instead of a graceful terminate signal I accidentally send a force kill signal?
### So we should check if the process with PID 778 was closed, by running:
```
ps 778
```
Essentially, SIGTERM is a "nice" signal. It just politely asks the process to close itself. And it's up to the process to also be polite, and listen to the request.

Which means, if the application is stuck in some way, then it might ignore SIGTERM, and not close itself. So it's time to bring out the big guns: The SIGKILL signal!

Method 2 – Forcefully Terminate the Process with SIGKILL

SIGKILL should only be used as a last resort. When there's just no other way to close that process. Why?

Because SIGTERM allows the application to save its data and close normally.
But SIGKILL is brutal. It instantly kills the process, without allowing it to save data, or wrap up whatever it was doing. Which brings a small chance of data corruption, or rather, incomplete data, if the process was working on something and it was killed in the middle of that.
Think of SIGTERM as normally shutting down your computer. And SIGKILL as pulling the power cable (and battery) from your computer, or pressing the reset button. Of course, the "computer" in this case is the process we're working with.

Either way, SIGKILL cannot be ignored by a process. So when we send a SIGKILL, it's usually a guarantee that the process will be closed. There can be exceptions in some edge-cases, but >99% of the times, it will work.

So, no other solution to terminate that misbehaving process? Alright. To force-kill a process on a Linux machine, run the sudo kill -s SIGKILL command, followed by the PID of the process. For something with PID 1367, we'd run:
```
sudo kill -s SIGKILL 1367
```
And, just as a reminder: If this was a process managed by systemd, and you need it back up, you can use its service name to start it again:
```
sudo systemctl start nginx.service
```
### How do I Kill a Process Based on its Name?
```
sudo pkill --signal SIGTERM nginx
```
### To forcefully terminate all processes with a certain name:
```
sudo pkill --signal SIGKILL nginx
```
For example, let's say we want to kill the process called bash. But if we search for entries with this name, look how many processes we might get:
```
pgrep -a bash
```
### How Do I Kill a Process Based on the Port Number It's Using?
```
sudo ss -ltunp
```
Regarding the -ltunp options passed to the ss command, this is what each letter means:

- l is for listening.
- t is for TCP.
- u is for UDP.
- n is for numerical (to show port numbers like "22" instead of names like "ssh").
- p is for process.
### To filter only processes that are listening on a certain port, like port 80, for example, we could pipe this output to grep:
```
sudo ss -ltunp | grep ':80'
```
### Or, to filter only processes listening on port 22, we could run:
```
sudo ss -ltunp | grep ':22'
```
