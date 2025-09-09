# 🔀 XSS (Cross-Site Scripting)
*Date: 2025-09-09*  
*Source: [DOM-based XSS](https://portswigger.net/web-security/cross-site-scripting/dom-based)*

---

## 📌 Overview
DOM-based XSS vulnerabilities usually arise when Javascript takes data from an attacker-controllable source. To deliver a DOM-based XSS attack, you need to place data into a source so that it is propagated to a sink and causes execution of arbitrary Javascript.

---

## 🔍 Key Concepts
- DOM-based XSS

---

## 🧠 Notes
- To test for a DOM-based XSS vulnerability, cybersecurity specialists usually rely on the use of Burp Suite's web vulnerability scanner. Alternatively, this vulnerabilty can be detected manually by testing every source turn in separately
- Testing HTML sinks
    - This is done by placing a random alphanumeric string into the source (such as `location.search`), then use developer tools to locate where the string appears. For each location the string appears, it needs to be manually investigated and understood, which may also change based on the browser as URL-encoding can change and block DOM-XSS vulnerabilities
- Testing Javascript execution sinks
    - Javascript execution sinks doesn't show up anywhere on the DOM, so you can't search for a string -- instead, you need to use Javascript debugger to determine whether and how your input is sent to a sink
    - For each potential source, we need to find references of its use on the page's Javascript code -- once you've found the reference, you can use the Javascript debugger to add a break point and follow how it is used
- Testing for DOM-XSS using DOM Invader
    - While using Burp's browser, you can also use its built-in DOM Invader extension to automate this process of investigating for DOM-XSS vulnerabilities
- For a website to be vulnerable to DOM-XSS attacks, it just needs to have an executable path via which data can propagate from source to sink; however, the way that happens depends a lot on the type of sinks and sources

---

## ⚠️ Common Vulnerabilities / Risks
- 

---

## ✅ Mitigation & Best Practices
- 

---

## 🧪 PortSwigger Lab Notes
- [Lab: DOM XSS in `document.write` sink using source `location.search` inside a select element](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-document-write-sink-inside-select-element)
    - By opening any product on the presented website, we can see that the URL includes an attribute `productId` which is used with a `document.write` which we can see through the browser's "Inspect Element" function
    - By adding an additional parameter `&storeId=Ga3Fef7` we can see that the website automatically implements an additional option on the `<select>` element
    - Knowing that, we can customize the `storeId` URL parameter to break out of the element with something like `&storeId="></select><img%20src=1%20onerror=alert(1)>`
- [Lab: DOM XSS in `innerHTML` sink using source `location.search`](https://portswigger.net/web-security/cross-site-scripting/dom-based/lab-innerhtml-sink)
    - With a similar fix to the lab above, we just need to add `<img src=1 onerror=alert(1)>` into the search box, which will trigger the alert
    - If wanted, we can solve this lab by following the same process as the lab above by adding a random string into the URL `search` parameter and customize it with `?search="></span><img%20src=1%20onerror=alert(1)>`