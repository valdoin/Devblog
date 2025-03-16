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

## {{< theme_text_color >}}Level 0→1.{{< /theme_text_color >}} 

![Level 1 Solution](/posts_images/overthewire-bandit/overthewire-bandit1.png)

Now that we’ve retrieved the password for level 1, it's time to switch to the next level. Each level requires you to log in as a different user using the password you just obtained.  
To proceed, log out of the current session and start a new SSH session using the next level’s credentials:
{{< highlight bash >}}
exit 
ssh bandit1@bandit.labs.overthewire.org -p 2220 {{< /highlight>}} 

Make sure to save each password as you progress, as you'll sometimes need to backtrack to previous levels to solve more advanced challenges ! 

---

## {{< theme_text_color >}}Level 1→2.{{< /theme_text_color >}} 

![Level 2 Solution](/posts_images/overthewire-bandit/overthewire-bandit2.png)

The correct way to open a file with a dash in its name by specifying its explicit path : 
{{< highlight bash >}} cat ./-filename {{< /highlight>}} 

or by prefixing with -- to stop option parsing : 
{{< highlight bash >}} cat -- -filename {{< /highlight>}} 

---

## {{< theme_text_color >}}Level 2→3.{{< /theme_text_color >}} 

![Level 3 Solution](/posts_images/overthewire-bandit/overthewire-bandit3.png)

Filenames with spaces can cause issues when using commands, they must be properly handled using quotes : 
{{< highlight bash >}} cat 'spaces in this filename'{{< /highlight>}} 

or espaces characters : 
{{< highlight bash >}} cat spaces\ in\ this\ filename{{< /highlight>}} 

---

## {{< theme_text_color >}}Level 3→4.{{< /theme_text_color >}}

![Level 4 Solution](/posts_images/overthewire-bandit/overthewire-bandit4.png)

In Unix systems, [hidden files](https://en.wikipedia.org/wiki/Hidden_file_and_hidden_directory) names begin with a dot, meaning they don't show up in a normal [ls](https://manpages.ubuntu.com/manpages/noble/en/man1/ls.1plan9.html) listing.  
Here I used tab completion which automatically completed the filename.  
There are more efficient ways to display all files including hidden ones : 
{{< highlight bash >}} ls -a {{< /highlight>}}

or : 
{{< highlight bash >}} ls -la {{< /highlight>}}

This one also shows permissions, ownership and file sizes.

---

## {{< theme_text_color >}}Level 4→5.{{< /theme_text_color >}}

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

## {{< theme_text_color >}}Level 5→6.{{< /theme_text_color >}}

The password for this level is hidden somewhere inside a directory. In order to find it, the file must be human-readable, exactly 1033 bytes in size and not executable.

![Level 6 Solution](/posts_images/overthewire-bandit/overthewire-bandit7.png)

I firstly came up with this listing but it's too long and unreadable so we must filter it further by specifying the file size :  

![Level 6 Solution](/posts_images/overthewire-bandit/overthewire-bandit8.png)

Much better !

---

## {{< theme_text_color >}}Level 6→7.{{< /theme_text_color >}}

The next password is hidden somewhere on the server. To locate this file, it must be owned by bandit7, be owned by group bandit6 and have a size of exactly 33 bytes.

![Level 7 Solution](/posts_images/overthewire-bandit/overthewire-bandit9.png)

The output is cluttered with 'Permission denied' and 'No such file or directory' messages making it hard to spot the correct file.  
To remove these unnecessary messages and only display relevant results I used [grep](https://manpages.ubuntu.com/manpages/noble/man1/grep.1.html) : 

![Level 7 Solution](/posts_images/overthewire-bandit/overthewire-bandit10.png)

And here's the password !

---

## {{< theme_text_color >}}Level 7→8.{{< /theme_text_color >}}

In this one, we have to find the password located in a text file next to the word 'millionth'.

![Level 8 Solution](/posts_images/overthewire-bandit/overthewire-bandit11.png)

It's a really long file, so again we must filter these lines using [grep](https://manpages.ubuntu.com/manpages/bionic/en/man1/grep.1.html) :

![Level 8 Solution](/posts_images/overthewire-bandit/overthewire-bandit12.png)

---

## {{< theme_text_color >}}Level 8→9.{{< /theme_text_color >}}

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

## {{< theme_text_color >}}Level 9→10.{{< /theme_text_color >}}

In this level, the password is hidden as one of the few human-readable strings and it is preceded by multiple '=' characters.

Since the file contains a mix of binary and text data we need to extract human-readable strings first.  
We can use [strings](https://manpages.ubuntu.com/manpages/trusty/man1/arm-none-eabi-strings.1.html) which filters out non-printable characters, and then search for lines containing '=' with [grep](https://manpages.ubuntu.com/manpages/bionic/en/man1/grep.1.html) : 
{{< highlight bash >}}strings data.txt | grep '==.*'{{< /highlight>}}  


The command quickly reveals the password : 

![Level 10 Solution](/posts_images/overthewire-bandit/overthewire-bandit15.png)

---

## {{< theme_text_color >}}Level 10→11.{{< /theme_text_color >}}

The solution to this level is quite straightforward, as you only need to decode the base-64 encoded content of the file to retrieve the password.  
You can do this using [base64](https://manpages.ubuntu.com/manpages/bionic/man1/base64.1.html) : 

{{< highlight bash >}}base64 -d data.txt{{< /highlight>}}

---

## {{< theme_text_color >}}Level 11→12.{{< /theme_text_color >}}

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

## {{< theme_text_color >}}Level 12→13.{{< /theme_text_color >}}

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
For example, I'm pretty sure [gzip](https://linux.die.net/man/1/gzip) expects files to end with .gz or the command won't work, so you'll sometimes need to rename your files with [mv](https://manpages.ubuntu.com/manpages/bionic/man1/mv.1.html).

![Level 13 Solution](/posts_images/overthewire-bandit/overthewire-bandit16.png)
![Level 13 Solution](/posts_images/overthewire-bandit/overthewire-bandit17.png)
![Level 13 Solution](/posts_images/overthewire-bandit/overthewire-bandit18.png)
![Level 13 Solution](/posts_images/overthewire-bandit/overthewire-bandit19.png)
![Level 13 Solution](/posts_images/overthewire-bandit/overthewire-bandit20.png)

This level is easy but almost drove me to madness due to the sheer number of nested extractions required to retrieve the password.

---

## {{< theme_text_color >}}Level 13→14.{{< /theme_text_color >}}

After logging into level 13, you can find a private SSH key file in the home directory, which means we'll need to use [ssh](https://manpages.ubuntu.com/manpages/jammy/en/man1/ssh.1.html) key-based authentification instead of the usual password login :
{{< highlight bash >}}ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220{{< /highlight>}} 

After connecting as bandit14, you are now able to read the password from the indicated location :
{{< highlight bash >}}cat /etc/bandit_pass/bandit14{{< /highlight>}} 


This challenge introduces SSH key-based authentication, where a private key file replaces password login. 

The {{< theme_text_color >}}-i{{< /theme_text_color >}} flag tells SSH to use a specific identity file (the private key) for authentication. In practice, SSH keys work as a pair - the private key (must be kept secret by the owner) and public key (stored on servers). When connecting, your SSH client uses your private key to sign a ["challenge"](https://en.wikipedia.org/wiki/Challenge%E2%80%93response_authentication). Then the server verifies this signature using the corresponding public key and if verified, you're authenticated without needing a password.
This method is both more secure and convenient than password authentication, as it eliminates the risks of password transmission and brute force attacks.

---

## {{< theme_text_color >}}Level 14→15.{{< /theme_text_color >}}

The goal of this level is to find the password for the next level using the 3000 port on localhost.
In order to interact with an open port, we can use [netcat](https://manpages.ubuntu.com/manpages/bionic/man1/nc_openbsd.1.html), which allows us to send and receive raw data over a network connection : 

![Level 15 Solution](/posts_images/overthewire-bandit/overthewire-bandit21.png)

(quick display of [nmap](https://manpages.ubuntu.com/manpages/xenial/man1/nmap.1.html), used to show which ports are open on localhost).


---

## {{< theme_text_color >}}Level 15→16.{{< /theme_text_color >}}

For this level, you need to establish a [SSL](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0,_2.0,_and_3.0) connection to the server on port 30001 using [openssl](https://manpages.ubuntu.com/manpages/trusty/man1/openssl.1ssl.html) and [s_client](https://manpages.ubuntu.com/manpages/trusty/man1/s_client.1ssl.html) :

{{< highlight bash >}}openssl s_client -connect localhost:30001{{< /highlight>}} 

After the connection is established, you can enter the current level's password, the server will answer with the password for next level.

This challenge introduces secure connections using SSL/TLS protocols.  

The [openssl s_client command](https://manpages.ubuntu.com/manpages/trusty/man1/s_client.1ssl.html) functions as an SSL/TLS client, allowing encrypted communication with servers.  
Unlike regular unencrypted connections (like [telnet](https://manpages.ubuntu.com/manpages/xenial/man1/telnet-ssl.1.html) or [netcat](https://manpages.ubuntu.com/manpages/bionic/man1/nc_openbsd.1.html)), OpenSSL establishes a secure channel where data is encrypted before transmission. This prevents anyone monitoring network traffic from seeing sensitive information like passwords.  

---

## {{< theme_text_color >}}Level 16→17.{{< /theme_text_color >}}

Firstly, we need to find which port between 31000-32000 had a server speaking SSL, then submit the current password to receive the SSH private key for the next level.

This indicates us we have to us [netcat](https://manpages.ubuntu.com/manpages/bionic/man1/nc_openbsd.1.html) :

![Level 17 Solution](/posts_images/overthewire-bandit/overthewire-bandit22.png)

You now have to test each port with [openssl](https://manpages.ubuntu.com/manpages/trusty/man1/openssl.1ssl.html), many won't work or just echo back your input, but port 31790 returns something different :

![Level 17 Solution](/posts_images/overthewire-bandit/overthewire-bandit23.png)
![Level 17 Solution](/posts_images/overthewire-bandit/overthewire-bandit24.png)

(N.B. : the {{< theme_text_color >}}-ign_eof{{< /theme_text_color >}} flag is crucial for this challenge as it prevents OpenSSL from closing the connection after sending data. Without this flag, the connection would close immediately after submitting the password (considered EOF, [End-Of-File](https://en.wikipedia.org/wiki/End-of-file)), not giving us time to receive the server's complete response).

After connecting to port 31790, I sent the current level's password and received an RSA private key in return.  
I saved this private key locally and used it to access level 17.

---

## {{< theme_text_color >}}Level 17→18.{{< /theme_text_color >}}

Initially, my connection to bandit17 was rejected because the private key file permissions were too open : 

![Level 18 Solution](/posts_images/overthewire-bandit/overthewire-bandit25.png)

SSH requires private keys to be protected, so you have to fix this using [chmod](https://manpages.ubuntu.com/manpages/trusty/man1/chmod.1.html) to set proper permissions(readable only by the owner) :

![Level 18 Solution](/posts_images/overthewire-bandit/overthewire-bandit26.png)

After connecting to bandit17, I found two files : {{< theme_text_color >}}passwords.old{{< /theme_text_color >}} and {{< theme_text_color >}}passwords.new{{< /theme_text_color >}}.  
I used [diff](https://manpages.ubuntu.com/manpages/xenial/man1/diff.1.html) to compare these files and identify the changes : 

![Level 18 Solution](/posts_images/overthewire-bandit/overthewire-bandit27.png)

The output shows that line 42 is different in both files.  
The string in {{< theme_text_color >}}passwords.new{{< /theme_text_color >}} is the password for next level (the second one).  
As you can see, I also retrieved bandit17 password using what we learnt in {{< theme_text_color >}}level 14{{< /theme_text_color >}}, since we only connected to bandit17 using its private SSH key, it could eventually be useful later.

---

## {{< theme_text_color >}}Level 18→19.{{< /theme_text_color >}}

Since the {{< theme_text_color >}}.bashrc{{< /theme_text_color >}} file prevents us from maintaining an interactive session, I executed the command directly during the SSH connection : 

![Level 19 Solution](/posts_images/overthewire-bandit/overthewire-bandit28.png)

This approach works because SSH allows executing commands directly on the remote server. 

---

## {{< theme_text_color >}}Level 19→20.{{< /theme_text_color >}}

After logging into level 19, you can find a binary named {{< theme_text_color >}}bandit20-do{{< /theme_text_color >}} in the home directory :

![Level 20 Solution](/posts_images/overthewire-bandit/overthewire-bandit29.png)

As suggested, running the binary with the {{< theme_text_color >}}id{{< /theme_text_color >}} command confirms that while running as bandit19, the binary executes commands with the effective user ID ([euid](https://eitca.org/cybersecurity/eitc-is-lsa-linux-system-administration/linux-processes/processes-overview/examination-review-processes-overview/what-is-the-difference-between-user-id-uid-and-effective-user-id-euid-in-linux-processes/)) of bandit20.
With this knowledge, I used the binary to read the password file for level 20, which is only accessible to the bandit20 user.  

This level demonstrates the concept of [setuid](https://manpages.ubuntu.com/manpages/trusty/en/man2/setuid.2.html) binaries, which allow users to execute programs with the permissions of the file owner rather than the current user.  
These can be both useful for legitimate purposes (like in this challenge) and potentially dangerous from a security perspective if not properly controlled.

---

## {{< theme_text_color >}}Level 20→21.{{< /theme_text_color >}}

For this challenge, we need to use the binary {{< theme_text_color >}}suconnect{{< /theme_text_color >}} that connects to a given port. If it receives the current level's password, it returns the password for the next level.

First, we need to set up [tmux](https://manpages.ubuntu.com/manpages/bionic/man1/tmux.1.html) to manage multiple terminal sessions on the server : 
{{< highlight bash >}}tmux new-session -d -s lvl20
tmux attach -t lvl20{{< /highlight>}} 

![Level 21 Solution](/posts_images/overthewire-bandit/overthewire-bandit30.png)

Then I check up which ports are free using [netcat](https://manpages.ubuntu.com/manpages/bionic/man1/nc_openbsd.1.html) and set up a listener on port 2000 that serves the current password. Meanwhile in another pane, I connected the {{< theme_text_color >}}suconnect{{< /theme_text_color >}} binary to this port. The program verified the password and responded with the next level password.

Setting up [tmux](https://manpages.ubuntu.com/manpages/bionic/man1/tmux.1.html) was crucial because we needed to run two commands simultaneously on the same SSH session. Without tmux, opening a second SSH connection would create a separate session environment where the netcat listener wouldn't be accessible to the first session.


---