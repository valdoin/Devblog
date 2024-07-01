+++
title = 'XSS Attacks'
date = 2024-06-26T11:08:42+02:00
draft = true
+++
I had always heard of XSS attacks by name, but I had never encountered one until I worked on my [portfolio](https://olivierandriko.com) last week. Here's what the Vue.js debugger warned me about :

![Vue Debugger Screenshot](../postsImg/xss1.png)

That's when I started looking into this kind of attack, understanding how it works, and eventually trying it out on myself.

## {{< theme_text_color >}}I. what ?{{< /theme_text_color >}}  

[Cross-Site Scripting](https://en.wikipedia.org/wiki/Cross-site_scripting) (XSS) is one of, if not the most common security vulnerabilities along with SQL Injection and MITM.  
It consists of injecting your own code into any poorly secured website, leading to data theft, session hijacking, defacement of websites and even distribution of malware.

## {{< theme_text_color >}}II. why ?{{< /theme_text_color >}}  

Now you may wonder "but then what makes my website vulnerable to XSS ?". Here are some common factors that can make a website susceptible to XSS attacks :

### {{< theme_text_color >}}Lack of Input Validation and Sanitization{{< /theme_text_color >}}  

- Directly Embedding User Input: If your website takes user input and directly embeds it into HTML, JavaScript, or other parts of the web page without proper validation or sanitization, it can be exploited.  

- Examples: Comment sections, search boxes, contact forms ...

### {{< theme_text_color >}}Improper Output Encoding{{< /theme_text_color >}}  

- Failure to Encode Output: Not encoding output correctly before rendering it in the browser can lead to XSS vulnerabilities. Different contexts (HTML, JavaScript, URL) require different encoding mechanisms.

- Examples: Displaying user-submitted content without HTML entity encoding or using [innerHTML](https://www.w3schools.com/jsref/prop_html_innerhtml.asp) to insert user data into the DOM without proper sanitization.

### {{< theme_text_color >}}Weak CSP{{< /theme_text_color >}}  

- Weak or No CSP: [Content Security Policy](https://developer.mozilla.org/fr/docs/Web/HTTP/CSP) (CSP) helps mitigate XSS by restricting the sources from which scripts can be loaded and executed. An absent or poorly configured CSP can leave your site vulnerable.

- Example : Make sure all your website comes from trusted sources :  
{{< highlight bash >}}Content-Security-Policy: default-src 'self' *.safe-source.example.net {{< /highlight >}} 

    this rule will ensure all content is provided by the site itself or by subdomains of safe-sourceexample.net.

## {{< theme_text_color >}}III. how ?{{< /theme_text_color >}}  