
**Summary**

A stored Cross-Site Scripting (XSS) vulnerability exists in the Glamour Salon Management System. This is an open source project on github developed by Hiruna Gallage designed to manage salon appointments, products, orders, and customer interactions. The vulnerability resides in the blog comment feature, where user input in not properly sanitized before rendering on the page. This allows attackers to store malicious JavaScript on the website and execute it in the browser of any users who visit that page.

- Vulnerability Type: Stored Cross Site Scripting (XSS)
- Affected File: `blog-details.php`
- Line: 65
- Severity: High
- Impact: Attackers are able to execute malicious JavaScript in a victim's browser


**Details**

I used the [Snyk](https://snyk.io/) tool, which is a security platform that helps developers find and fix vulnerabilities in their "code, open-source dependencies, container images, and infrastructure as code configurations". I specifically used the `snyk code test` command that tests source code for any security issues through static application security testing (SAST).

Upon running the command, I noticed the following issue:

```
┌──(root㉿kali)-[/home/kali/salon-management-system]

└─# snyk code test

...

✗ [High] Cross-site Scripting (XSS) 

   Path: blog.php, line 65 

   Info: Unsanitized input from an HTTP parameter flows into the echo statement, where it is used to render an HTML page returned to the user. This may result in a Cross-Site Scripting attack (XSS).
```


I then manually went into the `blog.php` file in the project to confirm the vulnerability and located it in line 65.

![](../XSS%20Dislcosure%20-%20Glamour%20Salon/attachments/VirtualBoxVM_2E1IIXvfaD.png)

`<p class="p-border"><?= $comment['content'] ?></p>`

Because the code renders the user submitted content without any escaping or sanitization of dangerous characters or phrases, it allows the attackers to inject malicious JavaScript.



**Proof of Concept (PoC)**

- Navigate to the blog comment section on any of the forums on the website
- Submit this payload as a comment `<script>alert("hacked")</script>`
- Refresh the page
- The injected code will execute in the browser and display an alert popup with the message "hacked"

![](../XSS%20Dislcosure%20-%20Glamour%20Salon/attachments/Pasted%20image%2020250407212811.png)

![](../XSS%20Dislcosure%20-%20Glamour%20Salon/attachments/Pasted%20image%2020250407212818.png)

**Impact**

According to [Portswigger](https://portswigger.net/web-security/cross-site-scripting/stored), an attacker exploiting XSS vulnerabilities may be able to:
- Impersonate or masquerade as the victim user.
- Carry out any action that the user is able to perform.
- Read any data that the user is able to access.
- Capture the user's login credentials.
- Perform virtual defacement of the web site.
- Inject trojan functionality into the web site.


**Remediation**

In order to mitigate this vulnerability, implement input sanitization where user content is submitted. In this case, use [PHP's `htmlspecialchars()` function](https://www.php.net/manual/en/function.htmlspecialchars.php) which encodes special characters into HTML entities, preventing the browser from interpreting them as executable code.

**Patches**

Unfortunately, a patch has not been released at the time of writing. Numerous attempts were made to contact the maintainer of the repository, but were unsuccessful.

I opened an issue on the repository which can be found here: https://github.com/hirunaofficial/salon-management-system/issues/1


**Timeline**

3-10-25: Vulnerability Discovered

3-14-25: Issue opened on Github Repository

3-15-25: Disclosed vulnerability to vendor's email

3-30-25: Submitted contact form on vendor's personal site

4-10-25: Official Write Up Published

TBD: Awaiting Patch Release

**Vendor / Maintainer Information**

Author: Hiruna Gallage

Website: https://hiruna.dev

GitHub: https://github.com/hirunaofficial/salon-management-system

Email: hello@hiruna.dev


**Contact Information**

Kyle Nguyen

kyletnguyen16@gmail.com
