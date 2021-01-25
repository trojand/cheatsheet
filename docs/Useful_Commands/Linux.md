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

[^1]: [Stack Overflow](https://stackoverflow.com/questions/3724786/how-to-diff-two-file-lists-and-ignoring-place-in-list)
[^2]: [StackOverflow](https://stackoverflow.com/questions/1583219/how-to-do-a-recursive-find-replace-of-a-string-with-awk-or-sed)
[^3]: [It's FOSS](https://itsfoss.com/recover-deleted-files-linux/)
