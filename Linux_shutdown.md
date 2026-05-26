# Linux Shutdown Command
### To shutdown Linux system instances immediately we can run:
```
sudo shutdown now
```
## When to Avoid Immediate Shutdown, and Schedule Instead
If a single person uses this server, we can certainly power it off this way. But if it's used by many people, then powering off immediately might be wrong.

On a bigger Linux machine, there might be multiple people logged in at the same time. So an immediate shutdown might abruptly disconnect everyone. People would wonder "What happened?" And some of their work in progress could be lost.

There's also another reason why we might not want to shutdown immediately. The server might provide certain services. For example, maybe this is a database server used by some website(s). Shutting it down at 5PM – when the website is heavily used – might not be the best idea. We might want to power it off at 4AM in the morning instead. When almost no one is using it. But maybe we're sleeping at that time, so we can't execute the Linux poweroff command manually.

However, the shutdown command has us covered. We can fix both of these issues with two superpowers of the Linux shutdown command:

The ability to schedule a shutdown at a future time. For example, we can tell the shutdown command to automatically power off the server at 4AM, while we're sleeping.
And we can also send a message to everyone connected to this server, telling them why it will be powered off at 4AM. This way, anyone connected to it will be informed about what will happen. And they'll have time to wrap off their work before the server shuts down.
So let's see the various ways we can shutdown Linux machines from the command line.
## The Syntax of the Linux Shutdown Command
We can see the manual of the shutdown command by typing this:
```
man shutdown
```
### Schedule a Shutdown at a Specific Time
```
sudo shutdown 04:15
```
### To cancel a scheduled shutdown, we can run this command:
```
sudo shutdown -c
```
### Set Wall Message with the Shutdown Command (Notify Users)
```
echo " I am working here. please do not interrupt me!"
```
