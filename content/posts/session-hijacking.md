+++
title = 'Session Hijacking'
date = 2025-02-19T19:34:34+01:00
draft = false
keywords = ["session hijacking", "cookies", "web security", "XSS", "man-in-the-middle", "session theft", "browser security"]
description = "Session hijacking is a well-known attack where hackers steal your session to impersonate you. Learn how it works, why cookies play a key role, and how to protect your users as a developer."
+++

Imagine logging into your bank account, email, or favorite social media site. You enter your password, hit login and you're in. 

But here’s the thing: after you log in, the website doesn’t ask for your password again every time you click a link.

That's because it remembers you and that's exactly what {{< theme_text_color >}}cookies{{< /theme_text_color >}} were made for.

## {{< theme_text_color >}}I. what ?{{< /theme_text_color >}}  

{{< theme_text_color >}}Cookies{{< /theme_text_color >}} are small pieces of data stored in your browser that keep you authenticated.  
But what if someone {{< theme_text_color >}}steals{{< /theme_text_color >}} your cookie? Well… then {{< theme_text_color >}}they become you{{< /theme_text_color >}}. 

This is {{< theme_text_color >}}session hijacking{{< /theme_text_color >}}, and it’s one of the most dangerous web security threats. 

## {{< theme_text_color >}}II. why ?{{< /theme_text_color >}}

Cookies are tiny text file that websites store in your browser. They help with : 

- {{< theme_text_color >}}Authentification{{< /theme_text_color >}} : keeping you logged in.

- {{< theme_text_color >}}Personalization{{< /theme_text_color >}} : Remembering your preferences (e.g., dark mode).

- {{< theme_text_color >}}Tracking{{< /theme_text_color >}} : Advertisers love them.

Most login-based websites use {{< theme_text_color >}}session cookies{{< /theme_text_color >}} to identify you. They contain a {{< theme_text_color >}}unique session ID{{< /theme_text_color >}} that proves you're logged in.

A cookie might store this after you log in : 
{{< highlight bash >}} session_id=abc123xyz; Secure; HttpOnly; {{< /highlight>}}

This {{< theme_text_color >}}session ID{{< /theme_text_color >}} tells the server that the user is authenticated.  
But if an attacker steals this cookie, they can use your account without needing your password.

## {{< theme_text_color >}}III. how ?{{< /theme_text_color >}}

Let’s take a look at the cookies stored in your browser right now.

{{< cookies >}}

If you click the "Show Cookies" button below and see "No cookies found (or blocked by security settings)," don’t be surprised !  
Unless you’re visiting shady websites, this is completely normal. It's a sign that the developers have done their job well.

As a developer, securing cookies is {{< theme_text_color >}}your responsability{{< /theme_text_color >}}.  
Here's how you can secure cookies and protect your users : 

- {{< theme_text_color >}}HttpOnly Attribute{{< /theme_text_color >}} : Add the HttpOnly flag to prevent JavaScript from reading cookies.
{{< highlight bash >}} Set-Cookie: session_id=abc123xyz; HttpOnly; {{< /highlight>}}

- {{< theme_text_color >}}Secure Attribute{{< /theme_text_color >}} : Cookies transmitted over [HTTP](https://en.wikipedia.org/wiki/HTTP) can be intercepted by attackers using [Man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) attacks, use the Secure flag to ensure cookies are only sent over [HTTPS](https://en.wikipedia.org/wiki/HTTPS).
{{< highlight bash >}} Set-Cookie: session_id=abc123xyz; Secure; HttpOnly; {{< /highlight>}}

- {{< theme_text_color >}}SameSite Attribute{{< /theme_text_color >}} : [Cross-Site Request Forgery](https://en.wikipedia.org/wiki/Cross-site_request_forgery) tricks users into making unauthorized requests using their valid session cookies. Set SameSite to restrict how cookies are sent : 
{{< highlight bash >}} Set-Cookie: session_id=abc123xyz; Secure; HttpOnly; SameSite=Strict; {{< /highlight>}}
Options :
    - {{< theme_text_color >}}Strict{{< /theme_text_color >}} : Cookies are only sent if the request originates from the same domain.
    - {{< theme_text_color >}}Lax{{< /theme_text_color >}} : Cookies are sent for top-level navigations, but not for third-party requests.
    - {{< theme_text_color >}}None{{< /theme_text_color >}} : Allows cross-site cookie usage.  

<br>
    
- {{< theme_text_color >}}Regenerate session IDs after login{{< /theme_text_color >}} : An attacker can exploit [session fixation](https://en.wikipedia.org/wiki/Session_fixation) by setting a session ID before login and using it afterward. You must regenerate sessions IDs upon authentification. Example in Express.js : 

![Express.js route handler for session regeneration](/posts_images/session-hijacking/session-hijacking1.png)

-  {{< theme_text_color >}}Set Expiration Time & Rotate Sessions {{< /theme_text_color >}} : If a session lasts indefinitely, a stolen session cookie remains valid forever. Set a short expiration time and regularly rotate session tokens.

{{< highlight bash >}} Set-Cookie: session_id=abc123xyz; Secure; HttpOnly; Max-Age=1800; {{< /highlight>}}

- {{< theme_text_color >}}Monitor & Invalidate Stolen Sessions{{< /theme_text_color >}} : If a user logs in from an usual location or device, prompt re-authentification. Make sure to store session metadata (e.g, IP, User-Agent) and detect anomalies. Example in Express.js : 

![Express.js session security check](/posts_images/session-hijacking/session-hijacking2.png)

## {{< theme_text_color >}}IV. conclusion.{{< /theme_text_color >}}

- Use {{< theme_text_color >}}HttpOnly{{< /theme_text_color >}} → Blocks JavaScript access to cookies.
- Set {{< theme_text_color >}}Secure{{< /theme_text_color >}} → Ensures cookies are sent only over HTTPS.
- Configure {{< theme_text_color >}}SameSite{{< /theme_text_color >}} → Prevents CSRF attacks.
- {{< theme_text_color >}}Regenerate{{< /theme_text_color >}} session IDs → Mitigates session fixation.
- {{< theme_text_color >}}Rotate{{< /theme_text_color >}} and {{< theme_text_color >}}expire{{< /theme_text_color >}} sessions → Limits attack windows.
- {{< theme_text_color >}}Monitor{{< /theme_text_color >}} and {{< theme_text_color >}}invalidate{{< /theme_text_color >}} sessions → Enhances security.

{{< theme_text_color >}}Session hijacking{{< /theme_text_color >}} is a serious security threat but by correctly configuring cookies, you can greatly reduce the risk and it is your job as a developer.