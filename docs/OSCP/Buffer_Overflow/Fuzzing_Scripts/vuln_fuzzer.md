# VULN_FUZZER.PY


Not too simple vulnerability fuzzer
Made for 1 CTF machine

```python
#!/usr/bin/python
import socket

### CHANGE THIS
rhost="<IP>"
rport=<PORT>


# Fuzzes input \x41\ up to 6000 bytes.
buffer = ["A"]
counter = 100
cmd_list = ["STATS", "RTIME", "LTIME", "SRUN", "TRUN", "GMON", "GDOG", "KSTET", "GTER", "HTER", "LTER", "KSTAN "]

while len(buffer) <= 30:
    buffer.append("A"*counter)
    counter = counter + 200

for cmd in cmd_list:
    
    for string in buffer:

        print "Fuzzing command %s with %s bytes" % (cmd, len(string))
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        connect = s.connect((rhost, rport))
        print repr(s.recv(1024))
        s.send(cmd+" "+string)
        s.close()
```

