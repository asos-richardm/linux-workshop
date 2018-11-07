# Linux Brown Bag

### Essential information
#### I keep getting this weird error message "blk_update_request: I/O error, dev fd0, sector 0"
Sorry this is my fault I didn't see this error until just before the workshop.  Your choices are to either ignore it or entering the follow commands:

```bash
sudo rmmod floppy
echo "blacklist floppy" | sudo tee /etc/modprobe.d/blacklist-floppy.conf
sudo dpkg-reconfigure initramfs-tool
```

Worth pointing out that this is a problem with Hyper-V setting up a floppy drive that doesn't exist rather than anything you'd experience on a real Linux system.

#### How do I navigate the directories?
`ls`: Equivalent to dir in DOS, lists the contents of a directory
`cd`: Equivalent to cd in DOS (except that cd.. becomes cd ..), use to change directories
`pwd`: Prints where you are in the directory structure to the console
>Hint: Remember that in Linux the root directory is / not C:\ or whatever D:\

#### It says I don't have permissions to do something? How do I fix that?
Normally you're running as your normal user with limited permissions, if you want to run a command as an administrator (called root in Linux) then you need to use `sudo` which can be invoked using `sudo <command you wish to run>`. You will find that you're asked for your user password, this is similar to how UAC (User account control) works in Windows.
>Note: Not every distribution supports sudo but it is well supported among the major distributions.

#### What's man do?

`man` is the command that brings up the information for a given command, invoked like `man <name of command>` e.g. `man ls`.  If `man` doesn't bring up any information try using `info` instead.

#### How do I display and edit files?
`cat` Dumps a file's contents to the console with no scrolling or paging.
`less` Displays the contents of a file allowing you to scroll and search, press `q` to quit!
`nano` A simple text editor that you can use to edit files.

#### What's bash?
When you're using the command prompt in Windows you're running an executable called cmd.exe which interprets your commands.  When you're using powershell you're effectively running a program called powershell.exe.  In Linux the program that interprets your commands is called the shell.  Today we're using a very popular shell called bash but it's worth noting that there are a lot of different shells available e.g. z-shell which some users prefer.

#### What's a distribution?
What we call Linux is actually just the kernel of the OS but there's much more to an operating system than the kernel.  Everything around the kernel varies from distribution to distribution but there is usually a resonable amount of overlap between the distributions.  Most of what you do today should be portable to almost every distribution.

## Tasks

### TASK: Update to the latest package managers using apt-get
### Difficulty: Easy

#### Introduction

If you've only only used windows before then Linux's package management system is a bit like a much better version of choclately for Windows.  If you've not used chocolately on Windows then the next best description would be NuGet for applications as well as libraries. Package management is actually one of the things that varies quite a bit from distribution to distribution are  but they all serve similar purposes so the principles you learn for apt (Ubuntu's package manager) should be applicable to other distributions.

#### Instructions

##### Listing installed packages

The package manager for ubuntu is called apt (Advanced Packaging Tool) and is usually invoked with a separate command to tell it what to do. Let's start with listing the installed packages.
```bash
apt list --installed
```
You probably won't recognize most of the packages but there are some that might seem familiar.

##### Listing and processing updates

Making sure you're up to date with the latest packages is easy in Linux, it's so easy in fact that I'm not going to tell you how to do it! See if you can use your ninja hacker skills to accomplish the following

1. (Optional) Get a list of packages that need to be upgraded
2. Upgrade packages that need it.

> Hint: To get started try either typing just `apt` on it's own or typing `man apt` to look at the manual page. You can also search in the manual page by pressing `/` typing your search query and pressing enter when done.
> Hint: You may need to use `sudo` if you don't know what that is then check the FAQ section at the top.

### TASK: Text kata

#### Introduction

If you attended the brown bag on Linux earlier in the year you may remember I mentioned the Unix philosophy:
 - Write programs that do one thing and do it well.
 - Write programs to work together.
 - Write programs to handle text streams, because that is a universal interfa

 This is a short practical exercise in how that plays out and you'll also learn something about bash in the process.

#### Instructions

Let's start with a program that does a single thing well, the date command which just prints the date and time of the system. Let's run it and see what it does.
```bash
date
```

This program only produces the date and time but we're going to look at a slightly contrived way of modifying the output so that it only shows the time.  To do this we're going to redirect the output of `date` command to be the input of `grep` command which finds and filters text.

```bash
date | grep -P '\d{2}:\d{2}'
```
 The `|` character here is what tells bash to use the output of date and use it for the input of the grep command.

This has got us some of the way but it's only highlighted the time and we want' to exclude anything that doesn't match the filter expression. If you run `grep --help` or `man grep` you should see how to be able to do this.  If you can't then check the spoiler below to keep you going.

<details>
<summary>Solution</summary>

```bash
date | grep -oP '\d{2}:\d{2}'
```

</details>

Now we've got our command, let's use it to create a script.  Try entering your solution into a file by invoking `nano showTime.sh`, saving the file using Ctrl+o and then exiting with Ctrl+x.

Run the file by entering 
```bash
./showTime.sh
```
>Hint: Capitalisation is important as Linux files names are case sensitive!

>Fun Fact: The file extension is not actually necessary. Unlike in Windows there are no explicit association between file types and how they are run in Linux but a lot of people include them to make it easy to tell what program should run a file.

Note that this is very similar to executing a powershell script, the main difference being that you get a message saying permission denied! This is because all files can only be executed if the correct permissions have been set.  Setting file permissions is outside the scope of this exercise but I'm putting the answer in a block below in case you want to look at the man page yourself to figure it out.

<details>
<summary>Solution</summary>

```bash
chmod u+x showTime.sh
```

To break this down for you:
 - chmod is short for change mode 
 - The three basic permissions are read/write/execute
 - The groups these can be assigned to are user/group/others
 - u+x means add the execute permission (represented by x) to the user who owns the file (represented by u)
 - The final argument is the file to change.

</details>

Now you've changed the permissions, if you re-run the script using `./showTime.sh` you should see it print the time.  We've now seen how to pipe the output of one program into another and how to create a very simple script file and set permissions on it.

#### Extension task

Display the 1st line of every file that ends with .log in the /var/log directory

<details>
<summary>Hints</summary>
Step 1: List all .log files in /var/log
Step 2: Find out how to use the `head` command to display the first line
Step 3: Use the `xargs` command to take the output of ls and use it as the parameters (not the input) for a different command.
</details>
<details>
<summary>Solution</summary>
ls /var/log/*.log | xargs head -n 1
</details>

### TASK: Setup key only SSH (Requires SSH client setup)

#### Introduction

If you're a security minded person you might have noticed that the password protecting the account is the strongest password ever.  If this machine was running on the open internet then anyone who could guess this password would have total control of your system.  You could change the password of course but then you'd have to enter it when you used the `sudo` command as well which would be quite inconvenient.  A better alternative might be to modify the SSH service so that you can't use username and password but instead have to use the much more secure SSH key method.

#### Instructions
First of all let's verify the problem in question so we know when we've fixed it, try SSHing into the server using your username and password.  How you do this will depend on which client you're using. Please ask if you need help with this.

So how do we change the configuration of the SSH service? The first thing to know is that processing running in the background in Linux are sometimes called daemons (pronounced demon) so that service that serves SSH connections is called sshd as it's the SSH daemon.

Configuration files in Linux are usually found in /etc see if you can find the file that contains the ssh daemon config and when you find the file and make the change just by reading the config file, if you get stuck the solutions are below.

>Hint: You will probably need to use sudo to change the file.
<details>
<summary>File Location</summary>
/etc/ssh/sshd_config
</details>
<details>
<summary>Setting location</summary>
The setting name is PasswordAuthentication and the value needs changing to no
</details>


Now we've changed the settings, we'll need to restart the service for the changes to take effect. You can do this using the following command:

```bash
systemctl restart sshd.service
```

Now try to connect to ssh to the box using the password. You should find that this doesn't work anymore and you can now only connect using the private key.