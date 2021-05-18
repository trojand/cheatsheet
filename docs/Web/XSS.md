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
