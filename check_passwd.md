# Linux Change User Passwd
On Linux, a user accounts is often simply called a "user"
In this tutorial we'll explore how to change:
- The password of the current user (the user we're currently logged in as).
- The passwords of other users.
- The password of the root user (the administrative user on Linux systems).
### How to Change the Password of the Current User
```
passwd
```
changing passwd for antor
Current password:
New passwd:
Retype new passwd:
passwd: passwd update successfully


or 
```
passwd john
```
Note the difference this time. It doesn't ask us what the current password for this user is. It jumps directly to choosing the new password. That's because the extra security check is not necessary now (checking if we know the correct password for this user). We're already logged in as root, so the system assumes we should have the power to change any password, for anyone, even if we do not know that user's current password.

Second of all, it makes sense. Although we are the "super user" called root, we might not know the current password for another user (and we should not know it anyway). But we should have the right to change it.

So we just type the new password, then type the new password again, and that's it. Job done, password was changed.

# Conclusion 
It doesn't matter if you're using Ubuntu, CentOS, Red Hat, Debian. These instructions should work the same way on (almost) any Linux-based operating system.
