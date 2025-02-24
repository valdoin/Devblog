+++
title = 'OverTheWire - Bandit'
date = 2025-02-24T21:44:15+01:00
draft = false
keywords = ["OverTheWire", "Bandit", "CTF", "Linux security", "ethical hacking", "cybersecurity challenges", "Linux commands", "hacking for beginners", "cybersecurity training", "command-line hacking"]
description = "A step-by-step journey through OverTheWire's Bandit challenge, a beginner-friendly wargame that teaches Linux security, command-line basics, and ethical hacking techniques."
+++

[OverTheWire's Bandit](https://overthewire.org/wargames/bandit/) is a beginner-friendly wargame designed to teach Linux basics, command-line navigation, and fundamental security concepts.  
I highly recommend it if you're looking to improve your understanding of file permissions, SSH, environment variables and basic scripting.  
In this article, I'll document my journey through the 34 levels of this challenge.

---

## {{< theme_text_color >}}Level 0.{{< /theme_text_color >}} 

The goal of this level is for you to log into the game using SSH. The host to which you need to connect is bandit.labs.overthewire.org, on port 2220. The username is bandit0 and the password is bandit0.  

The [ssh](https://manpages.ubuntu.com/manpages/noble/man1/ssh.1.html) command is straightforward to use : 

{{< highlight bash >}} ssh <username>@<remote> -p xxxx {{< /highlight>}} (replace xxxx with desired port number)  

With the given credentials : 
{{< highlight bash >}} ssh bandit0@bandit.labs.overthewire.org -p 2220 {{< /highlight>}} 

---

## {{< theme_text_color >}}Level 1.{{< /theme_text_color >}} 

![Level 1 Solution](/posts_images/overthewire-bandit/overthewire-bandit1.png)

Now that we’ve retrieved the password for level 1, it's time to switch to the next level. Each level requires you to log in as a different user using the password you just obtained.  
To proceed, log out of the current session and start a new SSH session using the next level’s credentials:
{{< highlight bash >}}
exit 
ssh bandit1@bandit.labs.overthewire.org -p 2220 {{< /highlight>}} 

Make sure to save each password as you progress, as you'll sometimes need to backtrack to previous levels to solve more advanced challenges ! 

---

## {{< theme_text_color >}}Level 2.{{< /theme_text_color >}} 

![Level 2 Solution](/posts_images/overthewire-bandit/overthewire-bandit2.png)

The correct way to open a file with a dash in its name by specifying its explicit path : 
{{< highlight bash >}} cat ./-filename {{< /highlight>}} 

or by prefixing with -- to stop option parsing : 
{{< highlight bash >}} cat -- -filename {{< /highlight>}} 

---

## {{< theme_text_color >}}Level 3.{{< /theme_text_color >}} 

![Level 3 Solution](/posts_images/overthewire-bandit/overthewire-bandit3.png)

Filenames with spaces can cause issues when using commands, they must be properly handled using quotes : 
{{< highlight bash >}} cat 'spaces in this filename'{{< /highlight>}} 

or espaces characters : 
{{< highlight bash >}} cat spaces\ in\ this\ filename{{< /highlight>}} 

---

## {{< theme_text_color >}}Level 4.{{< /theme_text_color >}}

![Level 4 Solution](/posts_images/overthewire-bandit/overthewire-bandit4.png)

In Unix systems, [hidden files](https://en.wikipedia.org/wiki/Hidden_file_and_hidden_directory) names begin with a dot, meaning they don't show up in a normal [ls](https://manpages.ubuntu.com/manpages/noble/en/man1/ls.1plan9.html) listing.  
Here I used tab completion which automatically completed the filename.  
There are more efficient ways to display all files including hidden ones : 
{{< highlight bash >}} ls -a {{< /highlight>}}

or : 
{{< highlight bash >}} ls -la {{< /highlight>}}

This one also shows permissions, ownership and file sizes.

---

## {{< theme_text_color >}}Level 5.{{< /theme_text_color >}}

In this level, we are given multiple files, but only one of them contains human-readable text. Our goal is to identify the right file and extract its content. 

{{< highlight bash >}} file ./-file00 ./-file01 ./-file02 ./-file03 ./-file04 ./-file05 ./-file06 ./-file07 ./-file08 ./-file09{{< /highlight>}}

This explicitly lists every file and checks its type using [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html). It's tedious and inneficient.  

Instead of manually listing all files, we can use [find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html) to locate all files and apply the file command to each one automatically : 
{{< highlight bash >}} find . -type f -exec file {} +{{< /highlight>}}
- type f → only look for regular files (ignore directories).
- exec file {} + → Run file on each file found.

You could also use brace expension to list the filenames more concisely : 
{{< highlight bash >}} file ./-file0{1,2,3,4,5,6,7,8,9}{{< /highlight>}}
This is only useful when filenames follow a clear pattern.

![Level 5 Solution](/posts_images/overthewire-bandit/overthewire-bandit5.png)

We can deduce that it must be {{< theme_text_color >}}file07{{< /theme_text_color >}}. Let's check :

![Level 5 Solution](/posts_images/overthewire-bandit/overthewire-bandit6.png)

And it does look like a password. On to the next level !

---

## {{< theme_text_color >}}Level 6.{{< /theme_text_color >}}

The password for this level is hidden somewhere inside a directory. In order to find it, the file must be human-readable, exactly 1033 bytes in size and not executable.

![Level 6 Solution](/posts_images/overthewire-bandit/overthewire-bandit7.png)

I firstly came up with this listing but it's too long and unreadable so we must filter it further by specifying the file size :  

![Level 6 Solution](/posts_images/overthewire-bandit/overthewire-bandit8.png)

Much better !

---

## {{< theme_text_color >}}Level 7.{{< /theme_text_color >}}

The next password is hidden somewhere on the server. To locate this file, it must be owned by bandit7, be owned by group bandit6 and have a size of exactly 33 bytes.

![Level 7 Solution](/posts_images/overthewire-bandit/overthewire-bandit9.png)

The output is cluttered with 'Permission denied' and 'No such file or directory' messages making it hard to spot the correct file.  
To remove these unnecessary messages and only display relevant results I used [grep](https://manpages.ubuntu.com/manpages/noble/man1/grep.1.html) : 

![Level 7 Solution](/posts_images/overthewire-bandit/overthewire-bandit10.png)

---