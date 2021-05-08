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

## Recursive sed
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



[^1]: [Stack Overflow](https://stackoverflow.com/questions/3724786/how-to-diff-two-file-lists-and-ignoring-place-in-list)
[^2]: [StackOverflow](https://stackoverflow.com/questions/1583219/how-to-do-a-recursive-find-replace-of-a-string-with-awk-or-sed)
[^3]: [It's FOSS](https://itsfoss.com/recover-deleted-files-linux/)
[^4]: [Fedora Project](https://docs.fedoraproject.org/en-US/quick-docs/how-to-edit-iptables-rules/)
[^5]: [Phoenixnap](https://phoenixnap.com/kb/iptables-tutorial-linux-firewall)
