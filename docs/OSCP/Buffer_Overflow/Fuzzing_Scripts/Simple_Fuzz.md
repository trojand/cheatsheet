# Simple_Fuzz.py

Simple Fuzzing script

```python
#!/usr/bin/python
import socket


buffer=["A"]
counter=100


while len(buffer) <= 30:
    #print("Buffer before append: %s" % len(buffer))
    buffer.append("A"*counter)
    #print("Buffer after append: %s" % len(buffer))
    counter=counter+200


for string in buffer:
    print "Fuzzing PASS with %s bytes" % len(string)
    s=socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    connect=s.connect(('<IP>',<PORT>))
    print repr(s.recv(1024))
    s.send('USER test\r\n')
    print repr(s.recv(1024))
    s.send('PASS ' + string + '\r\n')
    # Beware, sometimes it is better to shut the door (s.close()) without saying goodbye (exit, bye or QUIT)
    # s.send('QUIT\r\n')
    s.close()

```
