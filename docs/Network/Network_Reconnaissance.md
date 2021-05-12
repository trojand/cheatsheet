# Reconnaissance quick commands

## SMB Enum
### NMAP Commands
Note: Still a draft [^1]

```bash
nmap -n -sV --script "ldap* and not brute" -p 389 <DC_IP>
```

### smbver.sh
Know the version of SMB [^2]
```bash
#!/bin/sh
#Author: rewardone
#Description:
# Requires root or enough permissions to use tcpdump
# Will listen for the first 7 packets of a null login
# and grab the SMB Version
#Notes:
# Will sometimes not capture or will print multiple
# lines. May need to run a second time for success.
if [ -z $1 ]; then echo "Usage: ./smbver.sh RHOST {RPORT}" && exit; else rhost=$1; fi
if [ ! -z $2 ]; then rport=$2; else rport=139; fi
tcpdump -s0 -n -i tap0 src $rhost and port $rport -A -c 7 2>/dev/null | grep -i "samba\|s.a.m" | tr -d '.' | grep -oP 'UnixSamba.*[0-9a-z]' | tr -d '\n' & echo -n "$rhost: " &
echo "exit" | smbclient -L $rhost 1>/dev/null 2>/dev/null
echo "" && sleep .1
```
---
## Autorecon
```bash
sudo docker run --rm -it -v /root/Results/ACME/autorecon:/results -v ~/Scope/ACME/ips.txt:/root/ips.txt  tib3rius/autorecon -ct 2 -cs 2 -t ~/ips.txt --only-scans-dir -vv
```
---
## Host Discovery on the network using Windows Command-line

### Description
Usually used if full pivoting on the compromised host is not intended or is having issues

Make sure to replace "X.X.X"
```batch
FOR /L %i IN (1,1,254) DO ping -n 1 X.X.X.%i | FIND /i "Reply" >> c:\ipaddresses.txt
```

---
## View different databases using 1 tool
* dbeaver



[^1]: [PuckieStyle](https://www.puckiestyle.nl/smb-enum/)
[^2]: [HackTricks](https://book.hacktricks.xyz/pentesting/pentesting-smb)
