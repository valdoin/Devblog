+++
title = 'XSS Attacks'
date = 2024-06-26T11:08:42+02:00
draft = false
keywords = ['XSS','XSS attacks','Cross site scripting','website defacement','data theft','browser exploitation framework']
description = 'A comprehensive guide on XSS attacks: what they are, common vulnerabilities, and how to use tools like WebGoat and BeEF for testing and exploiting XSS vulnerabilities.'
+++
I've always heard of XSS by name, but I had never come across it until I worked on my [portfolio](https://olivierandriko.com) last week. Here's what the Vue.js debugger warned me about :

![Vue Debugger Screenshot](../postsImg/xss-attacks/xss1.png)

That's when I started looking into this kind of attack, understanding how it works, and eventually trying it out.

## {{< theme_text_color >}}I. what ?{{< /theme_text_color >}}  

[Cross-Site Scripting](https://en.wikipedia.org/wiki/Cross-site_scripting) (XSS) is one of, if not the most common security vulnerabilities along with SQL Injection and MITM.  
It consists of injecting your own code into any poorly secured website, leading to data theft, session hijacking, defacement of websites and even distribution of malware.

## {{< theme_text_color >}}II. why ?{{< /theme_text_color >}}  

Here are some common factors that can make a website susceptible to XSS attacks :

### {{< theme_text_color >}}Lack of Input Validation and Sanitization{{< /theme_text_color >}}  

- Directly Embedding User Input: If your website takes user input and directly embeds it into HTML, JavaScript, or other parts of the web page without proper validation or sanitization, it can be exploited.  

- Examples: Comment sections, search fields, contact forms ...

### {{< theme_text_color >}}Improper Output Encoding{{< /theme_text_color >}}  

- Failure to Encode Output: Not encoding output correctly before rendering it in the browser can lead to XSS vulnerabilities. 

- Examples: Displaying user-submitted content without HTML entity encoding or using [innerHTML](https://www.w3schools.com/jsref/prop_html_innerhtml.asp) to insert user data into the DOM without proper sanitization.

### {{< theme_text_color >}}Weak CSP{{< /theme_text_color >}}  

- Weak or No CSP: [Content Security Policy](https://developer.mozilla.org/fr/docs/Web/HTTP/CSP) (CSP) helps mitigate XSS by restricting the sources from which scripts can be loaded and executed. An absent or poorly configured CSP can leave your site vulnerable.

- Example : Make sure all your website comes from trusted sources :  
{{< highlight bash >}}Content-Security-Policy: default-src 'self' *.safe-source.example.net {{< /highlight >}} 

    this rule will ensure all content is provided by the site itself or by subdomains of safe-source.example.net.

## {{< theme_text_color >}}III. how ?{{< /theme_text_color >}}  
To demonstrate simple XSS attacks, I'll use the [WebGoat project](https://owasp.org/www-project-webgoat/) which is an intentionally  insecure web app that allows you to test vulnerabilities.  
I recommend using a virtual machine to run it and carefully reading the documentation beforehand.  

The easiest way to run the program is to pull its [Docker image](https://hub.docker.com/r/webgoat/webgoat) using this command : 
{{< highlight bash >}}docker pull webgoat/webgoat {{< /highlight >}} 

then expose the app on your 8080 port (you might need {{< theme_text_color >}}  sudo{{< /theme_text_color >}}   for both of these commands) :{{< highlight bash >}}docker run -p 127.0.0.1:8080:8080 -p 127.0.0.1:9090:9090 -e TZ=Europe/Amsterdam webgoat/webgoat{{< /highlight >}} 

If everything went well, you can now visualize the website on {{< theme_text_color >}}localhost:8080/WebGoat{{< /theme_text_color >}}. 

![WebGoat Login Page](../postsImg/xss-attacks/xss2.png)

Register yourself as a new user then head to the third page of the Cross Site Scripting (stored) section.

![Cross Site Scripting Section](../postsImg/xss-attacks/xss3.png)

See that comment text field down there ? That's where the magic is gonna happen.

![XSS Payload Exemple](../postsImg/xss-attacks/xss4.png)

As you can see, my comment appears harmless but actually loads a small script. While this script is not dangerous for the moment, it demonstrates that the text field lacks proper sanitization and allows us to run our own code into the website.

![XSS Script Loaded](../postsImg/xss-attacks/xss5.png)
![Harmless comment](../postsImg/xss-attacks/xss6.png)  

Since my comment is stored in database, the script will be loaded each time for every browser that accesses this page.

Here's a simple way to make it more malicious (simple [website defacement](https://en.wikipedia.org/wiki/Website_defacement)) :

![Website Defacement Script](../postsImg/xss-attacks/xss7.png)  

Result : 

![Website pwned](../postsImg/xss-attacks/xss8.png) 

This issue might be disturbing and humiliating for the website owner, but this is actually quite easy to fix and no harm has been caused to users.  
However XSS attacks can lead to data theft which is a much more significant problem.
I'll show you how this works using the [BeEF project](https://beefproject.com/). Once you've downloaded or cloned the project, you must run the following scripts from the root directory:
{{< highlight bash >}}./install{{< /highlight >}} (installs the required packages)
{{< highlight bash >}}./beef{{< /highlight >}} (launches the program)

*NB : it might ask you to change your credentials before launching BeEF, default credentials are both "beef".*

![BeEF credentials](../postsImg/xss-attacks/xss9.png) 
![BeEF menu](../postsImg/xss-attacks/xss10.png) 
In this menu, look for any {{< theme_text_color >}}Hook{{< /theme_text_color >}} URL then copy it. A hook in the BeEF Framework is a piece of JavaScript code that is used to collect data and perform actions in the victim's browser.
I will now inject this hook in every browser accessing the page we pwned before.

![Hook injection](../postsImg/xss-attacks/xss11.png) 

As you can see the hook got correctly loaded into the website :

![Inspect hook](../postsImg/xss-attacks/xss12.png) 

Now back to the BeEF terminal menu, copy the UI URL linked to the chosen hook

![Hook UI URL](../postsImg/xss-attacks/xss13.png) 

and paste it into your browser to access the attacker panel after entering the credentials you set up previously.

![Attacker panel](../postsImg/xss-attacks/xss14.png) 

This interface shows all the hooked machines along with their information. For example, here you can see the browser of my Ubuntu VM.

In the "commands" tab, among many other actions you can choose which page will replace the hooked one. In my case I chose a generic login form that could trick unexperienced users.

![Commands tab](../postsImg/xss-attacks/xss15.png) 

Here’s what the hooked comment section looks like on the victim's browser :

![Hooked comment section](../postsImg/xss-attacks/xss16.png) 

When the user submits their input, you can view the captured data in the attacker panel.

![Stolen credentials](../postsImg/xss-attacks/xss17.png) 

If your website stores sensitive information about your users, these credential leaks could be a disaster.

## {{< theme_text_color >}}IV. conclusion.{{< /theme_text_color >}}  

Never Trust User Inputs. That's it.