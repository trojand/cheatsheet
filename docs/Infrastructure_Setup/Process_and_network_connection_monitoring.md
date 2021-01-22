# Process and Network Monitoring (Linux)

## Description

* I use this to monitor established connections on a machine whether my Kali, target machine or pivot machine.
* This is to know new/established connections as well as lost ones
* It will also let me know if certain traffic/connections is coming other people on the box (In case of CTF boxes)
* I consider this improper and there are much better and more efficient ways of doing this



## Monitor Network 

* Monitor newly opened/lost/closed connections
```bash
while true; do sleep 1 && sudo netstat -punt > /dev/shm/current && diff --old-line-format="[+] %L" --new-line-format="[-] %L" --unchanged-line-format="" /dev/shm/current /dev/shm/before;mv /dev/shm/current /dev/shm/before;done
```
* Monitor if there are new listening connections
    * Only difference is in the netstat command
```bash
while true; do sleep 1 && sudo netstat -plunt > /dev/shm/current && diff --old-line-format="[+] %L" --new-line-format="[-] %L" --unchanged-line-format="" /dev/shm/current /dev/shm/before;mv /dev/shm/current /dev/shm/before;done
```
* Remove known processes you do not want to see
    * Difference is the grep command after netstat. Make sure to change the <PID>s
```bash
while true; do sleep 1 && sudo netstat -punt|grep -v -e <PID> -e <PID> -e <PID> > /dev/shm/current && diff --old-line-format="[+] %L" --new-line-format="[-] %L" --unchanged-line-format="" /dev/shm/current /dev/shm/before;mv /dev/shm/current /dev/shm/before;done
```

## Monitor new processes
```bash
journalctl -f
```
