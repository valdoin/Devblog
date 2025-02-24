+++
title = 'Password Hashing'
date = 2025-02-19T17:30:49+01:00
draft = false
keywords = ["password security", "password vulnerability", "hashing", "sha-256", "password hashing", "cryptography", "secure passwords", "storing passwords safely"]
description = "Storing passwords as plain text is a massive security risk. Learn why hashing is essential and how SHA-256 works."
+++

What happens when you sign up for a website ? Where does your password go ? And more importantly can someone see it ?
Turns out, if a website is poorly designed, the answer is yes. And that’s a huge problem.  
That's where {{< theme_text_color >}}hashing{{< /theme_text_color >}}   comes in.

---

## {{< theme_text_color >}}I. what ?{{< /theme_text_color >}}  

{{< theme_text_color >}} Hashing {{< /theme_text_color >}} is a process that takes your password and transforms it into a fixed-length string of characters, using a mathematical function.
Unlike encryption, which can be reversed with a key, hashing is a {{< theme_text_color >}}one-way process{{< /theme_text_color >}}, your original password is gone forever.

For example if you hash the password "password123" with [SHA-256](https://en.wikipedia.org/wiki/SHA-2), you get : 
{{< highlight bash >}} ef92b778bafe771e89245b89ecbcf99737512704f77a65a05a8b8d66f79152c4 {{< /highlight>}}

No matter how many times you hash "password123", you’ll always get the same result. But even the slightest change (like "Password123") creates a completely different hash.

---

## {{< theme_text_color >}}II. why ?{{< /theme_text_color >}}  

Storing passwords as plain text is a terrible idea. If a database gets hacked, every single user’s password is exposed. 

Imagine if Facebook, Google, or your bank stored passwords like this:

| {{< theme_text_color >}} Username {{< /theme_text_color >}}  | {{< theme_text_color >}} Password {{< /theme_text_color >}}        | 
| :---------------: |:---------------:|
| Alice  |   mypassword123 |
| Bob  | 123456         |  
| Charlie  | qwerty          |   

That means if hackers steal this database, they get instant access to millions of accounts.

Instead, websites should store hashed passwords:

| {{< theme_text_color >}} Username {{< /theme_text_color >}}  | {{< theme_text_color >}} Password {{< /theme_text_color >}}        | 
| :---------------: |:---------------:|
| Alice  |   e99a18c428cb38d5f260853678922e03 |
| Bob  | d41d8cd98f00b204e9800998ecf8427e         |  
| Charlie  | 098f6bcd4621d373cade4e832627b4f6         |   

---

## {{< theme_text_color >}}III. how ?{{< /theme_text_color >}}  

We'll take {{< theme_text_color >}} SHA-256{{< /theme_text_color >}}  as an example.  
At its core, SHA-256 (Secure Hash Algorithm 256-bit) is a cryptographic function that transforms any input into a 256-bit fixed-length output using a series of mathematical operations.  
Here’s a quick {{<  theme_text_color >}}  breakdown{{< /theme_text_color >}} of how it works:

- {{< theme_text_color >}} Bit Operations & Padding{{< /theme_text_color >}} : Your password is converted into binary, and padding is added to fit the block size of 512 bits.

- {{< theme_text_color >}} Message Compression{{< /theme_text_color >}} : The data is broken into 64 smaller chunks (words) and processed through a series of logical and bitwise operations (XOR, AND, OR, etc.).

- {{< theme_text_color >}} Hash Computation{{< /theme_text_color >}} : The data is mixed through 64 rounds of transformations using modular arithmetic and logical bit shifts. (refer to [Merkle–Damgård construction](https://en.wikipedia.org/wiki/Merkle%E2%80%93Damg%C3%A5rd_construction))

- {{< theme_text_color >}} Final Hash Output {{< /theme_text_color >}} : After all transformations, we get a {{< theme_text_color >}}64-character hexadecimal string {{< /theme_text_color >}} (which represents 256 bits).

To see how hashing works, enter a password below. We’ll hash it right here in your browser using {{< theme_text_color >}} SHA-256{{< /theme_text_color >}}.  
No data is sent to a server. Everything happens locally.  
{{< hash >}}

---

## {{< theme_text_color >}}IV. conclusion.{{< /theme_text_color >}}  

{{< theme_text_color >}}Hashing{{< /theme_text_color >}} is one of the simplest but most effective ways to secure passwords.  
If you're building a website, never store passwords as plain text.

---