---
title: Linux Basics
tags: [CS223, data structures, lab]

---

# Linux Basics

In this lab we will pick up with the setup at the end of the day 1 notes, repeated here for convenience:

- Go to https://github.com/Hanover-CS/CS223-day-1
- Look at Code > Codespaces > Create codespace on main
- VSCode editor areas
    - Explorer sidebar (left)
    - Terminal panel (bottom)
    - Main editor (top)
- Terminal commands:
    - `pwd` (where am I?)
    - `cd day1-stuff`  (change directory)
    - `ls`   (see what is there)
    - `ls -la`  (more details)
    - `python example.py`
    - `g++ example.cpp`  (compile our program)

## The file system

For this lab we will mostly work in the Terminal panel at the bottom. This gives you access to the "command line shell", where we can execute a lot of commands. Let's start by navigating around.

In computing we frequently work with files stored in a file system. The file system is a hierarchical structure of "folders" that contain in them other folders or specific files. In Linux we start at the "root" of the file system, denoted by a forward slash. So for example type the following:
```
ls /
```
and you will get a listing of the contents of this root of the system. Let's instead do a long listing:
```
ls -l /
```
Here is how one of the lines looks like:
```
drwxr-xrwx+   5 codespace root   4096 Jan 11 02:43 workspaces
```
Let's learn how to read this part. Let's start from the end, `workspaces` is the name of this item. Before it there is a last modification date, i.e. the last time something changed in this item.

Next to it is a number, `4096`. This is the number of bytes that this resource occupies. This is what we would normally write as `4KB`, for "kilo-bytes".

Now let's jump to the other side. The weird bit of `drwxr-xrwx+` gives us information about what kind of thing the item is and about who can access it. First of all the `d` says that this is a **directory**: It is a container for other things, and we can look in it if we like.

Following that there are three triples of `rwx`, possibly some of them replaced by a dash. To understand those we need to understand ownership:

In a Linux system we have users, and users are organized in groups: A user can belong to many groups, and a group can contain many users (this is often called a many-to-many relationship). Let's find out which user we are, type the following command:
```
whoami
```
You should see the answer `codespace`. So our "user name" when we use this system is `codespace`. Let's also find out which groups we belong to, that's the command `groups`:
```
groups
```
You should see a long list of groups. Don't worry too much about those right now.

Now, each file or directory has associated with it both a user and a group. Let's do our listing again:
```
ls -la /
```
But actually, don't type it again! Instead, use the up arrow, which takes you through the command history, until you find the one you need, then hit Enter.

Ok now look at the workspaces entry again. You should find near the middle the words `codespace root`. This means that this directory is **owned by** the user codespace, and also owned by the group root. `root` is basically the superuser group, so those with administrative powers on the machine belong to this group.
Look at the other files in the list. Who are their owners and groups?

Now 'let's go back to the bit at the beginning of the workspace line:
```
drwxr-xrwx+
```
You should read the bit after the `d` in three parts:
- `rwx`: This means the owner (the codespace user, i.e. us) can both **r**ead and **w**rite to this item, and can also e**x**ecute it. For files "execute" means to run the program, but for directories it means you can look inside.
- `r-x`: The second set of items refers to the group (the root group in our case). This group can read and execute (look inside), but they cannot write. Don't worry about why that is.
- `rwx`: The third set, towards the end and before the `+`, refers to everyone else. In this case every user in the system can read, write and execute this resource.

Let's take a look at another item. Find the row that says `usr`. You will find that it's owned by the root user and the root group. But the last three permission bits are `r-x`, which means everyone, including us, can look inside there. Let's do that: To look inside the `usr` directory we'll have to use its full name, which means we need to put the root slash in front, like so:
```
ls -l /usr
```
You should see as a result a number of directories. Note in particular one directory called `bin`. Let's take a look inside of it. Since this is "the bin directory within the usr directory", its full name is `/usr/bin`:
```
ls -l /usr/bin
```
You should see a very long list of items. These are actually all commands you can use! If you scroll up you'll find the `whoami` command we used earlier, you'll also find `python3`, and even the command `ls` itself! Almost every command we use from the command line is simply a file in the `/usr/bin` location (and some other locations, I'm oversimplifying).

You can find what precise location a command comes from by using the `which` command, like so:
```
which ls
```

### Moving around

At any given point in the terminal we are positioned inside a particular directory. This is called the **current directory**. There are two important commands to know in this context. First is **p**rint **w**orking **d**irectory, or `pwd`:
```
pwd
```
This should tell you something like `/workspaces/CS223-day-1` or `/workspaces/CS223-day-1/day1-stuff` if you followed some of the earlier steps. This means that we are currently located within, for example, the `day1-stuff` directory which is within the `CS223-day-1` directory within the `workspaces` directory. We can move around using the **c**hange **d**irectory command, or `cd`:
```
cd /workspaces/CS223-day-1
```
Whenever you type in a path name, it can get very tedious. Linux is there to help you out! As you type the part above, after you type the first few characters from `workspaces`, hit the Tab key and it will auto-complete it for you. This is really useful! First of all it saves you time, and second of all it can catch errors! If you typed something wrong earlier and there is no match, the auto-complete will fail and you'll know something is amiss.

So far you learned two useful shell features, practice using them:

- Up/down arrows go through previous commands
- Tab auto-completes command and file names

#### Absolute and relative paths

An important topic to learn about the filesystem is the concept of relative and absolute paths. The "path" is like the address for a resource. For example `/workspaces/CS223-day-1` is the path to the `CS223-day-1` directory. These are called **absolute paths**. They start with a slash, and describe the whole sequence of directories from the root down to our target, in this case `/` for root, then `workspaces` directory, then `CS223-day-1`.

Oftentimes it is helpful to use **relative paths**. A relative path does not start with a forward slash, and these paths are resolved *relative to your current directory*.

For example remember that right now we are in this `/workspaces/CS223-day-1` directory, confirm that by using the `pwd` command. (By the way, did you notice that the current directory is also printed in the shell as part of the prompt?). Do a quick listing to see that you have a directory `day1-stuff`. The full name (absolute path) for this directory would be `/workspaces/CS223-day-1/day1-stuff`. So we could switch to it by doing `cd` with that absolute path.

But instead, we can use a relative path! Relative to where we are right now, all we need is the `day1-stuff`. So we can instead say:
```
cd day1-stuff
```
And it will work just the same! This is the beauty of relative paths, a lot less typing needed. Use them when you can!

#### Two special symbols in paths

When we discuss paths, there are two important symbols, a single dot, `.` and two dots `..`. The single dot is easy to explain, it's another way to refer to the directory you are in right now. So in the previous section we could have instead done:
```
cd ./day1-stuff
```
This might seem useless in this case, but it has its uses in other situations.

The two dots instead means "go a step up". So for example, try the following, and check where you are and if that makes sense:
```
cd ..
pwd
cd ..
pwd
```

## Practice!

Play around, and navigate to find where different commands are, and what else is there.