# SMB Host discovery and enumeration


## NMAP Commands
Note: Still a draft [^1]

```bash
nmap -n -sV --script "ldap* and not brute" -p 389 <DC_IP>
```

---
## smbver.sh
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

[^1]: [PuckieStyle](https://www.puckiestyle.nl/smb-enum/)
[^2]: [HackTricks](https://book.hacktricks.xyz/pentesting/pentesting-smb)
