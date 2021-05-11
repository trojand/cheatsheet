# Simple easy to run packages in Python & Go

## Webservers

* Used for transmitting files conveniently
* Commonly used due to the limitations of default tools on Operating Systems (Windows->Linux vice versa) when passing files
* Ease quick cross-platform transmission and sharing
* More feautures than the well known "python3 -m http.server"

### Go

#### ProjectDiscovery - Simplehttpserver[^1]
* Installation
    ```bash
    sudo apt install golang-go -y
    GO111MODULE=on go get -v github.com/projectdiscovery/simplehttpserver/cmd/simplehttpserver
    ```
* Simple command for running
    ```bash
    simplehttpserver -https -upload
    simplehttpserver -https -upload -listen 0.0.0.0:8000
    ```
* Add basic secure alias to your .zshrc/.bashrc
    ```
    alias go_simplehttpserver='simplehttpserver -upload -https -basic-auth root:toor'
    ```
    * Run alias with `go_simplehttpserver`
* Upload a file via curl
    * Windows
    * Linux: Via curl[^1]



### Python

#### Python3 HTTPS Server
```bash
openssl req -nodes -x509 -days 7 -newkey rsa:4096 -keyout ./twisted.key -out ./twisted.crt
python3 -m twisted web --https=8888 -c ./twisted.crt -k ./twisted.key --path .
openssl x509 -fingerprint -sha256  -in ./twisted.crt
```


#### Python3 Web Server With Upload
```bash
wget https://gist.githubusercontent.com/touilleMan/eb02ea40b93e52604938/raw/b5b9858a7210694c8a66ca78cfed0b9f6f8b0ce3/SimpleHTTPServerWithUpload.py
python3 SimpleHTTPServerWithUpload.py
```

## SMTP

### Python

#### Python3 SMTPD
```bash
sudo python3 -m smtpd -n -c DebuggingServer 0.0.0.0:25
sudo python3 -m smtpd -n -c DebuggingServer 0.0.0.0:587
```

[^1]: [Upload files via Curl](../Useful_Commands/Linux.html#upload-files-via-curl)
