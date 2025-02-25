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

And here's the password !

---

## {{< theme_text_color >}}Level 8.{{< /theme_text_color >}}

In this one, we have to find the password located in a text file next to the word 'millionth'.

![Level 8 Solution](/posts_images/overthewire-bandit/overthewire-bandit11.png)

It's a really long file, so again we must filter these lines using [grep](https://manpages.ubuntu.com/manpages/bionic/en/man1/grep.1.html) :

![Level 8 Solution](/posts_images/overthewire-bandit/overthewire-bandit12.png)

---

## {{< theme_text_color >}}Level 9.{{< /theme_text_color >}}

The password is the only line that appears exactly once in the file.  
In order to do that we must use a combination of [sort](https://manpages.ubuntu.com/manpages/focal/man1/sort.1.html) and [uniq](https://manpages.ubuntu.com/manpages/xenial/man1/uniq.1.html).  
Since uniq only works on sorted input, we must first sort the file before filtering unique lines :

Sort the file to group identical lines together :
{{< highlight bash >}} sort data.txt{{< /highlight>}}

Remove duplicates : 
{{< highlight bash >}} uniq -u{{< /highlight>}}

![Level 9 Solution](/posts_images/overthewire-bandit/overthewire-bandit14.png)

Alternative approach by counting occurences : 

![Level 9 Solution](/posts_images/overthewire-bandit/overthewire-bandit13.png)

--- 

## {{< theme_text_color >}}Level 10.{{< /theme_text_color >}}

In this level, the password is hidden as one of the few human-readable strings and it is preceded by multiple '=' characters.

Since the file contains a mix of binary and text data we need to extract human-readable strings first.  
We can use [strings](https://manpages.ubuntu.com/manpages/trusty/man1/arm-none-eabi-strings.1.html) which filters out non-printable characters, and then search for lines containing '=' with [grep](https://manpages.ubuntu.com/manpages/bionic/en/man1/grep.1.html) : 
{{< highlight bash >}}strings data.txt | grep '==.*'{{< /highlight>}}  


The command quickly reveals the password : 

![Level 10 Solution](/posts_images/overthewire-bandit/overthewire-bandit15.png)

---

## {{< theme_text_color >}}Level 11.{{< /theme_text_color >}}

The solution to this level is quite straightforward, as you only need to decode the base-64 encoded content of the file to retrieve the password.  
You can do this using [base64](https://manpages.ubuntu.com/manpages/bionic/man1/base64.1.html) : 

{{< highlight bash >}}base64 -d data.txt{{< /highlight>}}

---

## {{< theme_text_color >}}Level 12.{{< /theme_text_color >}}

This one is a little bit more tricky, as it refers to the [ROT13 cipher](https://en.wikipedia.org/wiki/ROT13), a simple substitution cipher that shifts letters by 13 places.

A mapping of the cipher would look like this : 
| {{< theme_text_color >}} Original {{< /theme_text_color >}}  | {{< theme_text_color >}} Shifted (ROT13) {{< /theme_text_color >}}        | 
| :---------------: |:---------------:|
| a → n  |   n → a |
| b → o  | o → b         |  
| c → p  | p → c          | 
| ...  | ...        |  
| m → z  | z → m          |   

This indicated that we will need to use the [tr](https://manpages.ubuntu.com/manpages/trusty/en/man1/tr.1.html) command :

{{< highlight bash >}}cat data.txt | tr 'a-z' 'n-za-m' | tr 'A-Z' 'N-ZA-M'{{< /highlight>}}

{{< theme_text_color >}} Breakdown {{< /theme_text_color >}} : 

- 'a-z' / 'A-Z' respectively represents all lowercase/uppercase letters in order (you could also use [:lower:] and [:upper:]).

- 'n-za-m' / 'N-ZA-M' represents the same letters but shifted by 13 places → the letters a-m move forward by 13 places and the letters n-z wrap around back to the beginning.

---

## {{< theme_text_color >}}Level 13.{{< /theme_text_color >}}

Unlike previous levels, you'll need to create a directory since you will manipulate a few files.  
The [mktemp](https://manpages.ubuntu.com/manpages/trusty/man1/mktemp.1.html) -d command is used to create a temporary directory with a unique, hard-to-guess name, ensuring that other users on the system cannot easily find or interfere with it. This is useful when working with sensitive files, like the {{< theme_text_color >}}compressed hexdump{{< /theme_text_color >}} in this level.  
This will return a unique directory path, for example : 
{{< highlight bash >}}/tmp/tmp.XYZ123{{< /highlight>}}

Once the temporary directory is created, copy the data.txt file into it using [cp](https://manpages.ubuntu.com/manpages/trusty/en/man1/cp.1.html) :
{{< highlight bash >}}cp data.txt /tmp/tmp.XYZ123{{< /highlight>}}  

You can now head to the temp directory and rename the file we just copied using [mv](https://manpages.ubuntu.com/manpages/bionic/man1/mv.1.html) : 
{{< highlight bash >}}mv data.txt copy_data.txt{{< /highlight>}} 

The original given file was not a standard compressed file but instead a [hex dump](https://en.wikipedia.org/wiki/Hex_dump).  
A hex dump is a text-based representation of binary data, where each byte is shown as two hexadecimal digits. While it’s useful for inspecting raw data, a hex dump is not directly executable or decompressible, so we cannot simply use [tar](https://manpages.ubuntu.com/manpages/xenial/man1/tar.1.html), [gzip](https://linux.die.net/man/1/gzip), or [bzip2](https://manpages.ubuntu.com/manpages/jammy/man1/bzip2.1.html) on the hex dump. These tools expect binary files, not text representations.

The proper way to revert a hex dump to its original binary format is using : 
{{< highlight bash >}}xxd -r data.txt bin_data{{< /highlight>}} 

You then need to go through 7 steps of decompressing :

1. Extracting a {{< theme_text_color >}}.gzip{{< /theme_text_color >}} archive
2. Extracting a {{< theme_text_color >}}.bzip2{{< /theme_text_color >}} file nested inside
3. Handling another {{< theme_text_color >}}.gzip{{< /theme_text_color >}} compressed file
4. Extracting a{{< theme_text_color >}}.tar{{< /theme_text_color >}} compressed file
5. Another {{< theme_text_color >}}.bzip2{{< /theme_text_color >}} compression
6. Yet another {{< theme_text_color >}}.tar{{< /theme_text_color >}} archive
7. Finally, extracting the password from the last decoded {{< theme_text_color >}}gzip{{< /theme_text_color >}} file

Each step required checking the file type using the [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html) command and applying the correct decompression method.  
Note that some compression tools expect a specific file extension.  
For example, I'm pretty sure gzip expects files to end with .gz or the command won't work, so you'll sometimes need to rename your files with [mv](https://manpages.ubuntu.com/manpages/bionic/man1/mv.1.html).

![Level 13 Solution](/posts_images/overthewire-bandit/overthewire-bandit16.png)
![Level 13 Solution](/posts_images/overthewire-bandit/overthewire-bandit17.png)
![Level 13 Solution](/posts_images/overthewire-bandit/overthewire-bandit18.png)
![Level 13 Solution](/posts_images/overthewire-bandit/overthewire-bandit19.png)
![Level 13 Solution](/posts_images/overthewire-bandit/overthewire-bandit20.png)

This level is easy but almost drove me to madness due to the sheer number of nested extractions required to retrieve the password.

---