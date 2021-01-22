# Modifications on the Autorecon script to go further

Unsure if this still applies to the newer versions of autorecon

* SERVICE-SCANS.TOML

```bash
# Replace default password list for OSCP
#password_wordlist = '/usr/share/seclists/Passwords/darkweb2017-top100.txt' 
# or use Top 3 million passwords 
password_wordlist = '/usr/share/wordlists/rockyou-50k.txt'

# insert below the [[all-services.scan]]
    [[unicornscan-full-udp]]
    name = 'unicornscan-full-udp.service-detection'
    command = 'unicornscan -v -m U -p a {address}  >> "{scandir}/_unicornscan-full-udp.txt"'


```

* HTTP
    * Simply insert as additional scan

    ```bash
    [[http.scan]]
    name = 'http-vuln-scan-nmap'
    description = 'Nmap scans for HTTP vulnerabilities that could potentially cause a DoS if scanned (according to Nmap). Be careful:'
    command = 'nmap {nmap_extra} -sV -p {port} -Pn --script="http-vuln-*" --script-args="unsafe=1" -oN "{scandir}/{protocol}_{port}_http_nmap_vuln.txt" -oX "{scandir}/xml/{protocol}_{port}_http_nmap_vuln.xml" {address}'
    
    
    # Simply insert as additional scan
        [[http.scan]]
        name = 'wafw00f'
        description = 'Wafw00f to detect WAF'
        command = 'wafw00f -a {scheme}://{address}:{port} 2>&1 | tee  "{scandir}/{protocol}_{port}_http_wafw00f.txt" '
        
        
    # Simply insert as additional scan
        [[http.scan]]
        name = 'davtest'
        description = 'davtest to detect if WebDAV is turned ON'
        command = 'davtest -url {scheme}://{address}:{port} 2>&1 | tee  "{scandir}/{protocol}_{port}_http_davtest.txt" '
            

    # Simply insert as additional scan
        [[http.scan]]
        name = 'http-sqli-scan-nmap'
        description = 'Nmap scans for SQLi vulnerabilities'
        command = 'nmap {nmap_extra} -sV -p {port} -Pn --script=http-sql-injection -oN "{scandir}/{protocol}_{port}_http_nmap_sqli.txt" -oX "{scandir}/xml/{protocol}_{port}_http_nmap_sqli.xml" {address}'


    # In [[http.scan]] 
        name = 'nikto'
    # insert the '-C all' in the command= of nikto


    # In [[http.scan]] comment-out dirb and insert gobuster
        [[http.scan]]
        name = 'gobuster'
       command = 'gobuster -v dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u {scheme}://{address}:{port}/ -o "{scandir}/{protocol}_{port}_{scheme}_gobuster_dirbuster.txt" -l -t 50 -k --wildcard'
       
    ```

* SMB  
    * Simply insert as additional scan
    ```bash
    [[smb.scan]]
    name = 'smb-vuln-scan-nmap'
    description = 'Nmap scans for SMB vulnerabilities that could potentially cause a DoS if scanned (according to Nmap). Be careful:'
    command = 'nmap {nmap_extra} -sV -p {port} --script="smb-vuln-*" --script-args="unsafe=1" -oN "{scandir}/{protocol}_{port}_nmap_smb_vuln.txt" -oX "{scandir}/xml/{protocol}_{port}_nmap_smb_vuln.xml" {address}'


    [[smb.scan]]
    name = 'smb-version-139'
    command = '/bin/sh /root/Tools/NetworkAttacks/SMB/smbver_autorecon.sh {address} >> "{scandir}/smb-version-139.txt" '
    
    [[smb.scan]]
    name = 'smb-version-445'
    command = '/bin/sh /root/Tools/NetworkAttacks/SMB/smbver_autorecon.sh {address} 445 >> "{scandir}/smb-version-445.txt" '
    ```

