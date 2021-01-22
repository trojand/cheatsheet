# Code review commands

Just use a good IDE and you'll be fine. Else, see below:

## Grep Fu section
```bash
for i in $(grep -R <PATTERN_YOU_ARE_LOOKING_FOR>|cut -d : -f 1|sort -u); do echo "\n==========$i==========\n" && cat $i && read -s -d ' ' && clear;done
for i in $(grep -R <$PATTERN1>|cut -d : -f 1|sort -u); do echo "\n==========$i==========\n" && cat $i|grep -i <$PATTERN2> && read -s -d ' ' && clear;done
for i in $(grep -R <$PATTERN1>|cut -d : -f 1|sort -u); do echo "\n==========$i==========\n" && gedit $i;done
```

