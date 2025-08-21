# 🔀 XSS (Cross-Site Scripting)
*Date: 2025-08-21*  
*Source: [What is cross-site scripting (XSS) and how to prevent it](https://portswigger.net/web-security/cross-site-scripting)*

---

## 📌 Overview
Cross-Site Scripting is a web security vulnerability that allows an attacker to compromise the interactions that users have with a vulnerable application. XSS allows the attacker to masquerade as the victim user, so they can perform actions and/or gather data as the user.

---

## 🔍 Key Concepts
- Reflected XSS
- Stored XSS
- Dom-based XSS
- Burp Suite's web vulnerability scanner

---

## 🧠 Notes
- There are three main types of XSS attacks:
    - *Reflected XSS*, where the malicious script comes from the current HTTP request
    - *Stored XSS*, where the malicious script comes from the website's database
    - *DOM-based XSS*, where the vulnerability exists in client-side code rather than server-side code
- The *reflected XSS* is the simplest variety of this attack. It happens when the application received data through an HTTP request and inclues that data in an unsafe way -- a good example of this are cases where an user input is sent through the URL without hte proper verification
    - The original `https://insecure-website.com/status?message=All+is+well` could turn into `https://insecure-website.com/status?message=<script>/*Bad+stuff+here...+*/</script>`
    - If the user visits the URL constructed by the attacker, then the script executes in the user's browser
- The *stored XSS* (also known as persistent or second-order XSS) happens when an application receives data from an untrusted source and includes that data within its later HTTP responses in an unsafe way
    - For example, considering a chat application which allows users to send messages to others. If an user sends a message which gets converted to `<p>Hello, this is my message</p>`, this could be used to insert `<p><script>/* Bad stuff here... */</script></p>`
- *DOM-based XSS* happens when there's some client-sided Javascript that processes data from an untrusted source in an unsafe way, usually by writing data back to the DOM. For example, an application tha allows users to write on an input field and then proceeds to add this value into an HTML element
- The vast majority of XSS vulnerabilities can be found using *Burp Suite's web vulnerability scanner*
- To manually test for reflected and stored XSS, security engineers usually submit some simple unique input (such as a short alphanumeric string) into every entry point, to identify every location where the submitted input is returned in HTTP responses and test each location individually to determine whether suitably crafted input can be used to executed arbitrary Javascript
- To manually test for DOM-based XSS, we normally use a similar process by placing some unique input and then using the browser's developer tools to search the DOM for this input, and then testing each location to determine whether it is exploitable; however, other types of DOM XSS are harder to detect
- For example, to detect DOM-based vulnerabilities in non-URL-based input (such as `document.cookie`) or non-HTML-based sinks (like `setTimeout`), there is no substitute for reviewing Javascript code, which is time-consuming. Burp Suite's web vulnerability scanner reliably automates the detection of such vulnerabilities

---

## ⚠️ Common Vulnerabilities / Risks
- Impersonate/masquerade as the victim user
- Carry out any action tha the user is able to perform
- Read any data that the user is able to access
- Capture the user's login credentials
- Perform virtual defacement of the website
- Inject trojan functionality into the website

---

## ✅ Mitigation & Best Practices
- Ensure that all user input is *filtered* as strictly as possible depending on the expected information to be received and/or rules for a valid input
- When user-controllable data is output in HTTP, ensure it is encoded to prevent it from being interpreted as active content. Depending on the context, this may require applying combinations of HTML, URL, Javascript and CSS encoding
- To prevent XSS in HTTP responses that aren't intended to contain any HTML or Javascript, you can use the `Content-Type` and `X-Content-Type-Options` headers to ensure that browsers interpret the responses in the way you intend
- As a last line of defense, you can use *Content Security Policy (CSP)* to reduce the severity of any XSS vulnerabily that still occur

---

## 🧪 PortSwigger Lab Notes
- [Reflected XSS into HTML context with nothing encoded](https://portswigger.net/web-security/cross-site-scripting/reflected/lab-html-context-nothing-encoded)
    - This lab presented me with a simple blog along with a search mechanism. The goal is to exploit its vulnerability and lack of string filtering to generate a Javascript `alert()`
    - This can be easily solved by adding a the desired Javascript code -- for this case, I have used `<script>alert()</script>`
    - The desired response as a Javascript alert has been generated on `./reflected-xss-alert`
- [Stored XSS into HTML context with nothing encoded](https://portswigger.net/web-security/cross-site-scripting/stored/lab-html-context-nothing-encoded)
    - This lab had a very similar situation, where the user input is being treated in an unsafe way; however, due to the data persistence (keeping comments on a database), all we need to do is add a comment with the desired malicious Javascript code
    - The goal was to add an `alert()` which is executed everytime a new user accesses that blog, and this has been completed under `./stored-xss-alert` by adding `<script>alert()</script>` as a comment
- [DOM XSS in `document.write` sink using source `location.search`](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink)
    - This lab uses `document.write`, so we can perform a test by using an unique alphanumerical string, so we can locate every response for this input -- by doing that, we can see it was added as the `src` of an `img` on `./dom-xss-img`
    - Once we know it is present on an `img` element, we can search for something that breaks out of it and executes the script, such as `"><svg onload="alert()">`
    - That completes the lab, on the result `./dom-xss-alert`