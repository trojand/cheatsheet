# Code review & Regular Expression commands

* Used for manual code review
* A good IDE could also help in tracing once specific lines are found
    * A simple example of editors that could do this is Sublime Text and Notepad++ which can search through folders and use regex
* For more Regex references, see my [TEMPALTE.py](https://github.com/trojand/Script_Yard/blob/main/TEMPLATE.py)

## Grep Fu section
```bash
for i in $(grep -R <PATTERN_YOU_ARE_LOOKING_FOR>|cut -d : -f 1|sort -u); do echo "\n==========$i==========\n" && cat $i && read -s -d ' ' && clear;done
for i in $(grep -R <$PATTERN1>|cut -d : -f 1|sort -u); do echo "\n==========$i==========\n" && cat $i|grep -i <$PATTERN2> && read -s -d ' ' && clear;done
for i in $(grep -R <$PATTERN1>|cut -d : -f 1|sort -u); do echo "\n==========$i==========\n" && gedit $i;done
```

## Regex Section

### Targeted/Group Matching
```bash
perl -lne 'print $1 if /<R(E)GEX>/' < * 
perl -lne 'print "$1/$2" if /<R(E)GE(X)>/' < * 
perl -lne 'print "$1/$2" if /src=\"(.*?)\/(.*?)\//' blob.txt
for i in $(find .); do perl -lne 'print "$1/$2" if /src=\"(.*?)\/(.*?)\//' 2>/dev/null < $i;done|sort -u
```
