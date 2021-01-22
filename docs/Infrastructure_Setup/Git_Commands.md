# Local Git server sync commands

* Commiting code to your local Git server
	* It is recommended to get TLS/SSL working
	* Make sure to change

* `<domain>`
* `<username>`
* `<email>`
* `<commit_message>`

```bash
git -c http.sslVerify=false clone http://git.<domain>/<username>/scripts.git
git config --global user.email "<email>" && git config --global user.name "<username>"
git commit "<commit_message>"
git -c http.sslVerify=false push
```
