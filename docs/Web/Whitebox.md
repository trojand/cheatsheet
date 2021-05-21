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

