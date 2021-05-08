# Code review & Regular Expression commands

* Used for manual code review
* A good IDE could also help in tracing once specific lines are found
    * A simple example of editors that could do this is Sublime Text and Notepad++ which can search through folders and use regex
* For more Regex references, see my [TEMPLATE.py](https://github.com/trojand/Script_Yard/blob/main/TEMPLATE.py)

## Grep Fu section
```bash
for i in $(grep -R <PATTERN_YOU_ARE_LOOKING_FOR>|cut -d : -f 1|sort -u); do echo "\n==========$i==========\n" && cat $i && read -s -d ' ' && clear;done
for i in $(grep -R <$PATTERN1>|cut -d : -f 1|sort -u); do echo "\n==========$i==========\n" && cat $i|grep -i <$PATTERN2> && read -s -d ' ' && clear;done
for i in $(grep -R <$PATTERN1>|cut -d : -f 1|sort -u); do echo "\n==========$i==========\n" && gedit $i;done

grep -rnw --color "<Pattern>"
# To show only-matching
grep -rnw -o --color "<Pattern>" 
# To show only files containing tha pattern. Useful when there a lot of text in the grep
grep -rnw -l --color "<Pattern>" 
# To exclude certain file extensions.
grep -rnw --color "<Pattern>" --exclude "*.js"
```

## Regex Section

### Targeted/Group Matching
```bash
perl -lne 'print $1 if /<R(E)GEX>/' < * 
perl -lne 'print "$1/$2" if /<R(E)GE(X)>/' < * 
perl -lne 'print "$1/$2" if /src=\"(.*?)\/(.*?)\//' blob.txt
for i in $(find .); do perl -lne 'print "$1/$2" if /src=\"(.*?)\/(.*?)\//' 2>/dev/null < $i;done|sort -u
```

### Grouping[^1]
```
a(bc)           parentheses create a capturing group with value bc
a(?:bc)*        using ?: we disable the capturing group
a(?<foo>bc)     using ?<foo> we put a name to the group -> Try it!

To Test: https://regex101.com
```

### Email Regex
```
[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+
```

### URL Regex
```
https?://(www\.)?\w+\.\w+
```

### IP Address[^2]
```
cat * | egrep -o "(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)"
```

### Subdirectory finder
```
for i in $(find .); do perl -lne 'print "$1" if /https\:\/\/domain\.com\/(.*?)[\/\"]/'  2>/dev/null < $i;done|sort -u
for i in $(find .); do perl -lne 'print "$1" if /href\=\"(.*?)[\/\"]/'  2>/dev/null < $i;done|grep -v "http:" |grep -v "https:" | sed "s/\&\#32\;/\ /g" | grep -v "\.html" |sort -u
```


[^1]: [Factory Mind](https://medium.com/factory-mind/regex-tutorial-a-simple-cheatsheet-by-examples-649dc1c3f285)
[^2]: [Regex Tutorial](https://www.regextutorial.org/regex-for-ip-address-match.php)
