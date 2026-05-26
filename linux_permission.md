Let's make file and directory permissions on Linux easy to understand. How will we do that? By simply looking at:

Why do we need a permission?
What does it do?
And looking at a real example, where we run a command, experiment with that permission, and see its actual effects (screenshots included).
In this guide we'll focus more on how permissions work (the theory behind them). But if you're interested mainly in how to change permissions, rather than the theory, we also have a guide that will teach you how to use the chmod command on Linux.

How to List File and Directory Permissions on Linux
First thing's first: How do we see the permissions for our files and directories? With a simple ls (list) command:

ls -l
The -l flag (lowercase "l", not "L") tells ls to display a long-listing format. Since without that flag ls would just show a list of files and directories (no permissions). But with -l, we also get to see file / directory permissions, file sizes, and other useful info.

Let's look at some output. We run ls -l, we see this:


What does it mean?

Let's focus on two lines here: The one for the notes.txt file, and the one for the project directory.

-rw-rw-r-- 1 alex alex   35 Mar 12 01:08 notes.txt
drwxrwxr-x 3 alex alex 4096 Feb 17 02:46 project
First of all, how do we know which is a file, and which is a directory? Well, from the first character we see on each line. That stuff at the beginning:

-rw-rw-r--
drwxrwxr-x
Is where we actually see the permissions (the rwx stuff). Except for the first character. That first character tells us if the object we're looking at is a directory or a file.

- denotes that what we see on this line is a regular file.
d tells us, instead, that this is a directory.
There are other characters like l to show that this is a soft link (a kind of shortcut to another file / directory). Or b for block device, and other letters for other types. But we'll usually encounter regular files, or directories.

In this case:

-rw-rw-r-- 1 alex alex   35 Mar 12 01:08 notes.txt
drwxrwxr-x 3 alex alex 4096 Feb 17 02:46 project
We have - on the first line, showing us that notes.txt is a file. And d on the second line, signals that project is a directory. Now for the permission bits.

Understanding "rwx" Permissions for Files
Let's focus on how file permissions work on Linux. And later on we'll explore permissions for directories.

We now know that the first character shows us if this is a regular file, special file, or directory. But what about the rest of those characters?

-rw-rw-r-- 1 alex alex   35 Mar 12 01:08 notes.txt
On a line like this, what does rw-rw-r-- mean for our notes.txt file? Let's start with the letters:

r means the read permission is enabled.
w means the write permission is enabled. Without this, a user is not allowed to change / modify a file.
x means the execute permission is enabled. Required if we want to allow users to run a program, or script, or some kind of code included in a file.
- means the permission is disabled in this spot.
But how will we know what permission is actually disabled? Is it read, write, or execute?

Well, that's actually easy to tell. Because we have three groups of permissions, always in the same order. I will soon explain what each group applies to.

For example, if all permissions are enabled, for all three groups, we'll see:

rwxrwxrwx
But if we see something like this:

rw-r-----
Then some permissions are missing (disabled).

If we look at the first three characters, the next three, and the last three, then we can split rw-r----- into:

rw-
r--
---
And now we can figure out which permissions are disabled:

In the first set of three characters, an x is missing. Meaning execute permission is disabled for that first set.
In the second set, w and x are missing. So write and execute permissions are disabled here.
In the third set, all rwx characters are missing. Meaning all permissions are disabled. Read, write, and execute are not allowed.
Alright, now what does each set of permissions represent?


The first set refers to the permissions for the user that owns that file or directory.
The second set refers to permissions for the group that owns that file or directory.
And the third set refers to permissions for other users. Meaning anyone else that is not the user that owns that file, and not part of the group that owns that file.
Need more clarity? Ok, let's simplify with a practical example.

On my Ubuntu system I run this command to see permissions for my syslog file (from the /var/log directory):

ls -l /var/log/syslog
And this is the output I get:

-rw-r----- 1 syslog adm 135857 Mar 30 12:05 /var/log/syslog
Note that after the permission bits we can see a number 1 that is not important for our purposes. But after that we see syslog and adm here. These tell us:

The user that owns this file.
The group that owns this file.
So, in this case, the file is owned by a user called syslog and a group called adm.

Which means that now we can correlate the permissions, with the user owner, and group owner. Let's break down this line again:

-rw-r----- 1 syslog adm 135857 Mar 30 12:05 /var/log/syslog
The first set of permissions is rw-. Which means the user called syslog can read, and write to this file. But execute permission is disabled.
Second set of permissions is r--. Which tells us that anyone that is part of the adm group can read this file. But write and execute permissions are disabled.
In my case, my user is called alex and is part of these groups:


What permissions do you think I have for this file?

-rw-r----- 1 syslog adm 135857 Mar 30 12:05 /var/log/syslog
The user that owns the file is syslog. And the first set of permissions refer to the user that owns this file, syslog. But my user is alex, not syslog. So the first set of permissions, rw- do not apply to me.
Onto the second set of permissions, that refer to the group that owns this file, adm. alex is part of the adm group. As we can see in the screenshot above. Which means that the second set of permissions r-- should apply. My user, part of the adm group, should be able to read this file. And sure enough, that's true:
If I run a command to read the last few lines of this file:


It works.

But if I run a command to write to this file:


It fails. As expected, since I only have read permissions, but no write, or execute permissions. Remember, the second set of permissions were applied to my user, r--.

Read and write permissions are pretty intuitive. Reading the contents of a file is allowed or disallowed. Modifying (writing) to a file is allowed or disallowed. But what about the execute permission?

Well, on Linux we have programs that we can run. Or scripts (files which contain multiple lines of commands and instructions to be executed).

For example, imagine we have this script in a file called for.sh. Let's look at its contents:

cat for.sh

The permissions for this file are like this:


So it looks like the user called alex should be able to execute this file (we have x in all three sets of permissions). Let's specify the full path to this file and try to run it:

/home/alex/for.sh

Ok, it worked.

But if the file permissions look like this:


With no x executable permission enabled for the user, group, or others, we won't be allowed to execute this file:

/home/alex/for.sh

More of a visual learner? We also have a YouTube video where we cover Linux permissions (fast-forward to around 52:51 to find the section called "List, set, and change file permissoins):


Understanding "rwx" Permissions for Directories
In the case of directories, the effects of read, write, and execute permissions are slightly different.

r means we can read the contents of this directory: See what files and subdirectories it has inside.
w means we can "write" to this directory. That is, add more files and subdirectories inside. Or remove files and subdirectories from it. And, also, rename files or subdirectories inside.
x means we can "execute" the directory. More specifically, we can "step inside" this directory with a cd command.
Let's see some examples.

Imagine we have a directory called projects. And only one permission is allowed: The user that owns it is allowed to r read:

dr-------- 4 alex alex   4096 Jan  7 09:02 projects
That means we can run a ls command on it, to see its contents:

ls projects

We can see it contains two subdirectories: project1, and project2. But we do get some errors, because the ls command cannot do some stuff to see details about those subdirectories. We'll explore more about this soon, and how it can be fixed.

If we try to create a file in this directory, we can't (no write permission):

touch projects/newfile

If we try to "step inside" / switch to this directory with the cd change directory command, we can't, because we have no x execute permission:

cd projects

Normally, if a directory should be accessible to someone, both the read and execute permissions will be set:

dr-x------ 4 alex alex   4096 Jan  7 09:02 projects
Now the ls command will work normally when we check the contents of this directory (no more errors):

ls projects

And because x execute is enabled for our user, we can also "step inside" it:

cd projects

But we still can't create files (or subdirectories) in here, because the w write permission is disabled:


Also, because we have no w write permission, we can't delete files or directories in here either:


And we can't rename the files, or subdirectories it contains. For example, if we try to rename the project1 subdirectory to something like old_project:


We also get a "Permission denied" error.

But if the directory has the w write permission enabled:

drwx------ 4 alex alex   4096 Jan  7 09:02 projects
Then we can do all of the above:

We can write files to it.
We can create subdirectories inside.
We can remove files or subdirectories.
We can rename files or subdirectories inside.
Here are the contents before we do all of this:


Just the two subdirectories that existed before.

And here are the contents after we create a new file, add a new subdirectory, remove the project2 subdirectory, and rename project1 to old_project:


No more "Permission denied" errors because the directory is writable (for the user that owns it).

Of course, the same effects apply if these bits are enabled/disabled in the second set of permissions (for the group owner of the directory):

drwxrwx--- 4 alex alex   4096 Apr  3 21:52 projects
The second rwx means that anyone in the group called alex will be able to read, write, and execute this directory.

And if we add rwx in the last group, then all other users can also read, write, and execute this directory. "Other users" meaning:

A user that is not the same as the owner of this directory. In this case, any user that is not called alex.
And a user that is not part of the group that owns this directory. In this case, any user that is not part of the group called alex.
Both conditions have to be true. For example, if a user called john tries to access this directory, but john is part of the group called alex, then group permissions will apply to him (second set of permissions, the second rwx). Only if john is also not part of the group called alex then the third set of permissions (permissions for other users) will apply to him.

drwxrwxrwx 4 alex alex   4096 Apr  3 21:52 projects
In this case, everyone (user, group, others) has the same rwx permissions.

But we can also do stuff like:

Give our user+group full rights.
While making the directory read-only for other users.
All we need to do is:

Add rwx in the first two sets of permissions.
And just r-x (without w) in the third set of permissions:
drwxrwxr-x 4 alex alex   4096 Apr  3 21:52 projects
Which means:

First set is rwx: The user called alex will be able to read, write, execute.
Second set is rwx: A user part of a group called alex will be able to read, write, execute.
Third set is r-x: So a user that is not alex, and also not part of a group called alex will only be able to read and execute, but not write. Meaning, read-only permissions for others.
How to Calculate Permissions in Octal Values
Sometimes, instead of permissions like rw-rw-r-- we might see 664. Here's an example.

I create a file called simply newfile:

touch newfile
I set the permissions to rw-rw-r--:

chmod u=rw,g=rw,o=r newfile
Now if we look at the classic permissions for this file, we can see it's indeed set to rw-rw-r--:

ls -l newfile

But to see permissions represented in octal values, we can use the stat command:

stat newfile
And the important part from the output is this:


This line:

Access: (0664/-rw-rw-r--)  Uid: ( 1000/    alex)   Gid: ( 1000/    alex)
Tells us that the "octal permission" for our file is 0664. And we can ignore the first 0 if we aren't using any special permissions, and we're using just the usual rwx, read, write, execute permissions. We'll discuss what these optional, special permissions can be, at the end of this blog (SUID, SGID, and sticky bit permissions).

Now back to our "octal permissions" (The correct terms are "permissions represented in octal values"). Why are these equivalent? How do we get from rw-rw-r--to 664?

Think of regular rwx permissions like on and off switches. We can turn r, w, and x on or off (1 or 0). So when we have something like this:

rw-
It means r is on, w is on, and x is off.

Now if we represent this with 0 and 1, where 0 means off and 1 means on, we get this:

rw-
110
rw- = on, on, off = 110.

110 is a binary number made out of 3 bits. That's how the computer actually inspects the permissions for a file, or directory. It looks at these 3 bits, sees which is 0, which is 1, and it knows which permissions are enabled, and which are disabled.

And if we transform 110 from binary to the decimal system we use every day, 110 in binary is 6 in decimal.

How do we calculate this? Well, first we think about which permission bits are off, and which are on. For example, if we have:

rw-
Then r and w are on, x is off. So we get:

110
Next, to transform this from binary to decimal we can create a sum in our heads. The sum starts from 0 and then we continue this way:

If the first bit is 1 then we add 4.
If the second bit is 1 then we add 2.
If the third bit is 1 then we add 1.
For every bit that is off (set to 0) we don't add anything.
Otherwise said:

If r read permission is active then add 4.
If w write permission is active then add 2.
If x execute permission is active then add 1.
In our case, for:

rw-
110
We would add 4, because the first bit for r is 1 (on).
Then we add 2 because the second bit for w is 1 (on).
Finally, the third bit for x is 0 (off) so we don't add anything here.
The final result is 4+2=6. That's why rw-, which is 110 in binary, is equivalent to 6 in decimal.

But wait, why are permissions represented this way called octal? Well, because the smallest value is 0, when all rwx permissions are set to 0, or off. And the largest value, when all permissions are on, is 4+2+1=7. Which means we have 8 possible values (or to be more specific, digits): 0, 1, 2, 3, 4, 5, 6, 7. With 8 possible digits, that's octal notation. Just like binary has only 2 digits, 0 and 1.

But since the largest value can never be larger than 7, and 7 in octal is also equal to 7 in decimal, we can think of these as decimal values too.

Here's a table we can use as a cheatsheet to transform permissions from the regular "rwx" format to octal values:


rwx

rwx

rwx

rwx

rwx

rwx

rwx

rwx

Binary Value

000

001

010

011

100

101

110

111

Resulting Permission

---

--x

-w-

-wx

r--

r-x

rw-

rwx


Octal Value

0

1

2

3

4

5

6

7

Now, remember, we have three groups of permissions: for the user, for the group, and for other users. Which means that we'll have to apply this transformation – from rwx to octal values – three times.

As an example, something like this:

rw-rw-r--
Is equivalent to this in binary:

110 110 100
And according to our algorithm, to transform from binary to octal (or decimal):

For the first group: r is on, we add 4. w is on, we add 2. x is disabled (set to ) so we add nothing. Finally, 4+2=6
For the second group it's the same thing, so 4+2=6.
And for the third group, r is on so we add 4. But w and x are off, so we add nothing for those spots. Which means the final sum for the third group is 4.
So one way we can visualize our final result:

rw- rw- r--
110 110 100
420 420 400 (we add only the bits that are set to "1")
4+2 4+2 4+0
6   6   4
That's why rw-rw-r-- is equivalent to 664.

Can you figure out what rwxr-xr-x is in octal?

rwx = 4+2+1 = 7
r-x = 4+0+1 = 5
r-x = 4+0+1 = 5
Or, to visualize it:

rwx r-x r-x
111 101 101
421 401 401 (we add only the bits that are set to "1")
4+2+1 4+1 4+1
7     5     5
So rwxr-xr-x is 755 in octal.

Now let's see why, and when, this alternate notation can be useful.

For example, if we want to set permissions to rwxr-xr-x on our file, we would run a command like this:

chmod u=rwx,g=rx,o=rx newfile
But notice how long that command is. And a bit complicated to write too, prone to mistakes as we have to insert a few = signs and , commas in the right spots.

Now think about the alternate notation. rwxr-xr-x is equivalent to 755 in octal. So we can tell our chmod command to set the permissions to 755:

chmod 755 newfile
See how easy it is to write it this way?

Join 1M+ Learners
Learn & Practice DevOps, Cloud, AI, and Much More — All Through Hands-On, Interactive Labs!

Create Your Free Account
And if we look at our permissions in the classical rwx notation:

ls -l newfile

Note how setting permissions to 755 led to the same result: rwxr-xr-x. So these commands are equivalent:

chmod u=rwx,g=rx,o=rx newfile
chmod 755 newfile
Octal notation might be more convenient if we often need to use the chmod command, and if we need to set permissions to an exact specification.

How Linux Decides What Permissions to Apply to a User
The way permissions are interpreted can be a bit counter-intuitive. Here's an example:

I have a file called list.txt. Here are the permissions for it:

----rw-r-- 1 alex alex 35 Mar 30 12:32 list.txt
So the file is owned by a user called alex. And it's owned by a group also called alex. You'll often see this on most files and directories, where the user owner, and the group owner have the same name.

Anyway, I'm trying to access this file while I'm logged in as this exact user called alex. And alex is part of the group called alex. Will this command to read the file work?


----rw-r-- 1 alex alex 35 Mar 30 12:32 list.txt
We might be inclined to think "Well… yeah." Right? Since we can see the second set of permissions is rw-. Which means that anyone part of the group called alex should be able to read and write.

Well, check this out:


It's a fail. I cannot read this file. Why is that?

Because Linux applies the first set of permissions that match. So it does something like this:

First, it checks: Is the user trying to access this object the same one as the user that owns this object? If the answer is no, it jumps to step 2.
Is the user trying to access this object part of the group that owns this file? If the answer is no, it jumps to the last step.
Ok, then the last set of permissions will apply.
So what happens in our case?

----rw-r-- 1 alex alex 35 Mar 30 12:32 list.txt
Linux goes through this logic:

Is the user trying to access list.txt the same one as the user that owns this object? Yes. alex is trying to access this file. And alex is the owner of this file.
And bam! That's it. That's the set of permissions that will apply. The first set, because it was the first match.

Linux won't continue to the permissions for the group, or for other users. If permission set #1 matches, then it won't get to set #2, or #3. If #2 matches, then it won't get to #3.

And we can see that the first set of permissions is ---. Those are the permissions for the user that owns the file. And the owner of the file is alex. Which means that when we try to access this file as the user called alex we won't have any permissions – no read, no write, no execute.

The irony is that if we try to access this file as a random user, someone that is not alex, then we will be able to read this file. Why? Well, imagine a user called john tries to access the same file:

----rw-r-- 1 alex alex 35 Mar 30 12:32 list.txt
The permission logic will now go something like this:

Is the user trying to read this file the same as the user that owns this file? No. john is not alex, so jump to the next step.
Is the user trying to read this file part of the group called alex? No, the user called john is part of some groups, but not part of the alex group. So the second set of permissions does not apply either. Which means we jump to the next, and final part.
Apply the permissions for other users.
And the third set of permissions is r-- so john will be able to read this file.

Random user has more permissions than the owner of the file. Which can feel counter-intuitive. And when we see that the third set, permissions for others is r--, it can feel like "Hey, then this means that ALL users will be able to read this." Which, as we saw, is not always the case.

Of course, these are "weird" permissions we set for this file, just to use as an example of how permissions are interpreted. In the real world, we would rarely see a scenario where the owner has zero permissions, while other users have more than the owner.

Who Can Change the Permissions of a File or Directory?
Under normal circumstances, only two users can change the permissions:

The user that owns that file, or directory.
The administrative user called "root".
So for a file like this:

----rw-r-- 1 alex alex 35 Mar 30 12:32 list.txt
Only the user called alex, or the user called root can change the permission bits.

Which means that, ironically enough, even though alex cannot read or write to this file, he can fix this. He can simply run a command like this to add the read and write permissions for the user owner of this file:

chmod u+rw list.txt
And now the permissions are different:


So alex is able to read this file:


And since it can sometimes be useful: How do we change the owner of a file, or directory? With the chown command.

However, only the administrative "root" user can change the owner of a file or directory.

And it makes sense. If any user could change the owners, then people could "steal" each other's files. A user called john could set himself as the owner of files belonging to alex. And by becoming their owner, he could then change permissions, and read files that should not be readable by anyone else but alex.

So to change the user that owns a file, we need to either temporarily log in as root, or precede our command with sudo. Since anything we type after sudo will be executed with the root user's privileges.

To make a user called john the owner of a file called newfile we would run a command like this:

sudo chown john newfile
And now newfile is owned by john:


But, remember, there's also a group that owns this file. So we can change both the user owner, and the group owner, if we type both of these, separated by a : colon character, in the form of:

user:group
To change the user owner to john, and the group owner to adm, we would run this command:

sudo chown john:adm newfile
And now both of these were changed:


To change only the group owner, we can omit the user before our : colon character. And it's worth noting that if our current user is part of that group, then running the command as root is not necessary anymore.

For example, my current user called alex is part of the lxd group. So to change the group owner to lxd for a file called list.txt, this is the command we could run:

chown :lxd list.txt
And now the file is owned by the group called lxd.


If we get an error like this:

chown: changing group of 'newfile': Operation not permitted
It means our user is not part of that group. And the solution is to run the same command with root privileges (add sudo at the beginning):

sudo chown :lxd list.txt
And since we might forget that we need to add : when we want to change just the group, there's an alternate command that can change the group owner, chgrp. And it has a simpler syntax (does not require :).

Instead of:

sudo chown :lxd list.txt
We can run this to make lxd the group that owns the file list.txt:

sudo chgrp lxd list.txt
Special Permissions: SUID, SGID, and Sticky Bit
There are three "advanced" permissions besides the regular rwx we learned about. These are SUID, SGID, and sticky bit.

SUID is also called setuid. And SGID is also called setgid.

An easy way to remember SUID, and SGID is to think of them as "set user id" (SUID) and "set group id" (SGID). What these do depends on where we apply them. As they have a certain effect on files, and a different effect on directories.

SUID, and SGID Effects on Files
Whenever we run an executable program on Linux, the process that opens up is normally owned by the same user that executed that program.

Which means:

If alex runs that program,
then alex will own the process that starts up.
SUID changes this behavior. If SUID is active, then the user that owns that file will also be made the owner of the process that starts up.

This means that:

If alex runs that program,
but john owns that file,
then the process that starts up will be owned by john.
alex starts the program, but john owns the process.

Here's an example that will make this easier to understand.

We can run a program called ps. This program will show us the processes active under this current user session. And we can pass an argument u to the ps command. Which will make it display information in a "user-oriented format". A fancy way to say it will show us user-related information about the processes it displays on-screen.

So if we run this:

ps u
We get this:


It shows us three processes here. The bash process which hosts our user session, and the ps process we just launched.

Now notice the last line. It shows us that ps is running "under" our alex user. Which is to be expected. Since we're currently logged in as this user called alex.

Simple way to put it: alex launches a program, alex owns that process.

Now let's see how SUID changes this behavior.

First, let's copy the ps program (from the /bin directory) to our local home directory. So we can make some modifications to the file's owner and permissions:

cp /bin/ps $HOME
If we inspect permissions now, they look like this:


In this case a user called alex is the owner. Let's change the owner to john. Remember that we need root privileges to change the owner, so we add sudo in front of our command.

sudo chown john ps

💡
Note: It's very important to first change the owner, and then enable the SUID permission. If we do it in the reverse order, Linux, as a security measure, disables the SUID permission.
Now that john is set as the owner of the ps file we can add SUID in the mix.

To enable the SUID permission on this ps file we can run a command like chmod u+s ps. u+s roughly translates to "for the user set of permissions add (+) the s (SUID) permission". So the other permissions remain the same, but we just add this one on top of the older, pre-existing permissions.

But the more complete way to add this permission is to run:

chmod u+sx name_of_file
That's because for SUID (s) to be useful, it also needs the x (executable) permission to be active.

And since we are trying to change permissions for a file that we do not own anymore, we need to add sudo in front of our command:

sudo chmod u+sx ps
Now the permissions look like this:


Note how instead of x we see s here, to show us that the file has the SUID permission enabled.

So what does this do? Remember that the last time we run the ps u command we got this:


alex launched ps, alex owned the process called ps.

Now if we run our local ps file, from Alex's home directory, it has the owner set to john and the SUID permission enabled. So we get this result:

/home/alex/ps u

Note the difference. alex launched the ps program. But this time, alex does not own the process anymore. Instead, the owner of the file, john is the owner of the process that starts up. alex executed the file, but john owns the process.

When SUID is set on a file, it makes the owner of that file the owner of the process that starts up.

And here's a "secret". That's how we can, as a regular user called alex, execute commands with root privileges. When we run something like:

sudo apt upgrade
The apt program runs as the root user. How is that possible? Because the sudo utility has this same SUID permission enabled:

ls -l /bin/sudo

See? SUID enabled, and the owner of the file is root. So no matter which user runs this file, it will be executed as if the root user started it up.

Of course, the sudo program also verifies that the user is allowed to run sudo commands. But as soon as a user runs sudo, the program runs as the root user.

sudo ps u

And it has a knock-on effect on everything it starts up. We can see here:

alex starts sudo.
sudo runs as the root user.
sudo then starts the ps program.
ps "inherits" the same user. So ps will also run as the root user.
And that's how we can run programs as another user called root with the magic of the SUID permission.

Now let's move on to SGID. SGID does the same thing as SUID. But instead of a user owning that process, the group that owns the file will be the group that owns the process.

To set SGID on a file we can run two commands. First, we change the owner to whatever group we want to own the process that starts up:

sudo chown :group_name name_of_file
Or the equivalent command:

sudo chgrp group_name name_of_file
Then, for g group permissions, we add + the s (SGID) and x executable permissions:

sudo chmod g+sx name_of_file
Just to learn an additional thing: If the file is not in our current directory, we can also specify the full path to the file. Instead of ps we can address it by its full path: /home/alex/ps.

In our case, to change the group owner to john, and add SGID on our ps file from Alex's home directory:

sudo chown :john /home/alex/ps
sudo chmod g+sx /home/alex/ps

Note how when we changed the group owner, the previous SUID permission was disabled. Every time we change the user, or group owner, SUID is reset (disabled). That's why changing owners should always be done BEFORE enabling SUID.

We can tell ps to show us the user, and group that owns each process, plus the command that started each process by passing the o (user-defined format) option, followed by the name of the fields we want: user,group,command. And we specify the full path to the executable file, /home/alex/ps to run our local copy instead of the system-wide ps executable from /bin/ps:

/home/alex/ps o user,group,command

As expected:

alex starts the program.
alex user owns the process (since SUID is not enabled this time).
But instead of alex group, the process is now owned by the john group.
Because the john group owns the ps file. And SGID is set. So the group that owns this file will be the same group that owns the process: john.

To remove SUID, we can run:

chmod u-s name_of_file
And to remove SGID, we can run:

chmod g-s name_of_file
With sudo in front if our user is not the owner of that file.

For example, our current ps file looks like this:


With SGID enabled. To disable SGID we run:

sudo chmod g-s ps

# Or if we want to specify full path to file:

sudo chmod g-s /home/alex/ps
And the s changes into x to show that SGID was removed, and only the executable permission remains for the group-level permissions:


On most Linux systems, the sticky bit has no effect on files (just on directories).

SGID and Sticky Bit Effects on Directories
Let's create a new directory to study the effects of SGID and sticky bit:

sudo mkdir /web_project
And let's allow all users to write to this directory (for the o other users, we + add the w write permission):

sudo chmod o+w /web_project
Now what happens if a user called alex logs in, switches to this directory, and creates a file in here, called file1?

cd /web_project
touch file1
Who will own this file? The user that created it, along with the user's primary group. In this case alex:alex:

ls -l

And if a user called john logs in, steps into that directory, and creates a file called file2? Of course, the same will happen. The file will be owned by the user that created it and its primary group john:john:


Now let's bring SGID in the mix.

To add SGID to a directory:

chmod g+s /path/to/directory
Or, if we want to make sure that the directory is also group-executable, we can add an x in there too:

chmod g+sx /path/to/directory
Since SGID is not useful without the executable permission.

To remove SGID from a directory:

chmod g-s /path/to/directory
With sudo in front of the commands if the current user is not the owner of that directory.

In our case, let's change the group that owns this directory to www-data:

sudo chown :www-data /web_project
And we can add SGID to our /web_project directory with this command:

sudo chmod g+s /web_project
Now in an ls -l command our directory's owners and permission bits look like this:

drwxr-srwx   2 root www-data  4096 Apr  3 22:17 web_project
So what changes? Remember the default behavior (without SGID)?


A user alex created a file. And the user's primary group, also called alex, was set as the group that owned that file. Same for john.

But when SGID is enabled? Let's see.

While logged in as alex let's step into this directory:

cd /web_project
And create a new file called file3:

touch file3
If we run ls -l, here's the difference:


alex is in the primary group called alex. But this time, the group owner for file3 is www-data instead of the group called alex.

When SGID is set, the group that owns the directory is set as the group owner of the files / subdirectories created inside. Not the primary group of the user that created that file / subdirectory.

For example, we can imagine here that we create these files for a web project. And the web server (daemon / process) runs under a user and group called www-data. By automatically making the group owner of any files created here www-data this web server process will have full read-write access to these files, by default. Since the group owner has rw- permissions in this case.

If the group owner would be alex instead of www-data, the web server application would not be able to write to these files, just read them (last set of permissions for other users would apply, which is r--).

Also, if all of our web developers are in the www-data group, they will be able to modify these files, no matter who created them. Although we would also have to add the g+w permission to the web_project directory if that would be the case. To allow anyone in the www-data group to write to this directory.

💡
Note: There are scenarios where permission changes might automatically disable SGID on a directory. So it would be ideal if, first, the permissions are set accordingly, and SGID added after that. Of course, we can add SGID again if it is lost after a permission change. Just something to be aware of.
Now let's move on to the sticky bit.

Think about a shared directory like this. As long as users have write permissions for this directory, they can modify it in any way. That includes deleting anything they want in here.

So alex could delete John's file:


A slight mistake here: I didn't need to add -r to the rm command. That's for removing directories. But it works on files too, even if -r is not required for those.

How do we fix this? With the sticky bit.

To add the sticky bit to a directory we can run a command like this:

chmod o+t /path/to/directory
To remove the sticky bit:

chmod o-t /path/to/directory
With sudo in front of these commands if the current user does not own that directory.

In our case, to add the sticky bit to /web_project we will run:

sudo chmod o+t /web_project
And the permission bits for this directory would look like this:

drwxr-srwt   2 root www-data  4096 Apr  3 22:48 web_project
The last t in there shows us that the sticky bit is enabled.

Now let's assume John added his file2 back to this directory, after it was accidentally deleted by Alex.


With the sticky bit set, this is what will happen if a user will try to delete a file that does not belong to him / her:

rm file2

file2 belongs to John, so Alex cannot remove it.

But if alex tries to delete file3, which belongs to him:


Then that will work.

But it's not just to prevent deletion of files. The sticky bit has these effects on directories:

Users can delete only the files they own.
Users can delete only the subdirectories they own.
Users can modify only the files they own.
Users can rename only the files, or subdirectories they own.
A useful set of properties for shared directories like these. An example where this is used by default is the /tmp directory where temporary files are stored.

drwxrwxrwt  11 root root      4096 Apr  3 21:24 tmp
Note the last t and the permission bits (rwx everywhere).

Everyone has full access to this, to be able to write temporary files (manually, or automatically by the programs they are using). With full access, anyone should be able to modify the contents of this directory. Which means that, normally, users could "step on each other's toes." Or an application could accidentally delete another user's stuff when it tries to clean up its temporary files. But with the sticky bit these kinds of problems are avoided. Users will only be able to delete and modify only the stuff they actually own.

Conclusion
Phew, long story, wasn't it? But that's about everything important to know about how file and directory permissions work on Linux.

A reminder: We also have this guide, if you want to follow-up on this blog and also learn how to use the chmod command on Linux.

Alexandru Andrei
Alexandru Andrei
You can find Alexandru's tutorials on DigitalOcean, Linode, Alibaba Cloud. He believes the Linux ecosystem is simple, elegant, and beautiful. He's on a mission to help others discover this Linux world.