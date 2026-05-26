# How to List All Users in Linux
Operating systems based on the Linux kernel support multiple user accounts. Instead of "user accounts" they're often just called "users".

So let's see how to list all of these users / accounts. First, we'll explore the commands. And at the end of this blog we'll explain how the commands work.
### List All Users on Linux
```
getent passwd | awk -F: '{ print $1 }'
```
This list is quite long. Do we really have so many user accounts on our system? Which person will log in as the user called "www-data" on this machine?

Well, no actual human being will log in as "www-data". But a program like the Apache web server will use that account to do some of its work.

Which means some of these users / accounts are meant to be used by programs. And others are meant for actual human beings. Linux-based operating systems call these:
- system accounts
- user accounts

### List Regular (Non-System) User Accounts on Linux
```
getent passwd | awk -F: '($3>999){ print $1 }'
```
### List Users that Belong to a Certain Group on Linux
On a system with many user accounts we might want to filter based on groups. Otherwise said, show only those users that belong to a certain group.

For example, maybe we want to see which users have administrative privileges. To do that, we can list users belonging to the "sudo" group:
```
getent group sudo | awk -F: '{ print $4 }
```
### Explaining the Commands
```
getent passwd | awk -F: '{ print $1 }'
```
### Understanding the 'getent passwd' Command
```
cat /etc/passwd
```
or
```
getent passwd
```
So, why getent passwd? Because, in some cases, user / account information might not be stored entirely in the /etc/passwd file. On some systems – for example those that use LDAP – some user information can be stored on a separate server.

Which means, you will get some user information from the /etc/passwd file. But not all of it. Because some user accounts will be defined on a separate server, somewhere. So taking a look at the /etc/passwd file might only give you partial data. While getent passwd will show you the full picture:

User data stored locally, in files like /etc/passwd.
But also user data stored on LDAP servers, or other remote places. (As long as the Linux machine is configured to use those as sources for additional user accounts).

which:
- Retrieves all user information available, with getent passwd.
- Sends (| pipes) the output to awk.
- Tells awk that the field separator is :.
- And then instructs awk to print just the first field (the username, in this case).