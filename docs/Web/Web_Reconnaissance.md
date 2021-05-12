# Web Reconnaissance and Content Discovery

## Content Discovery
### KiteRunner[^1]
```
kr scan <hosts-file> -w routes-large.kite -x 20 -j 100 --fail-status-codes 400,401,404,403,501,502,426,411
~/Tools/kr scan https://subdomain.domain.com  -A=apiroutes-210328 --ignore-length=34 -x 10 --output text --profile-name domainTarget --ignore-length 0
kr wordlist list
kr brute <hosts-file> -w wordlist.txt -e asp,aspx,cfm,xml -x20 -j250 -A=apiroutes-210228
```
### JSFScan.sh
* Scans for endpoints and shows them in a nice format [^2]
    * build on a docker instead so there is no need to install stuff on your local system
        * basically sh script of a bunch of tools.
        * Nice little WebUI though
* Try to compare with gospider

### Param Miner[^3]

## Web Tech Discovery
### ProjectDicovery - HTTPX
```
httpx -tech-detect -x all -status-code -title -ip -http2 -cdn  -l ~/Scope/subdomains.txt
```


## Wordlists
* [Assetnote](https://wordlists.assetnote.io/)

[^1]: [Assetnote - Kiterunner](https://blog.assetnote.io/2021/04/05/contextual-content-discovery/)
[^2]: [Github - KathanP19](https://github.com/KathanP19/JSFScan.sh)
[^3]: [Burp Suite - Param Miner](https://github.com/PortSwigger/param-miner)
