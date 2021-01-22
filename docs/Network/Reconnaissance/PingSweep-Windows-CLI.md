# Host Discovery on the network using Windows Command-line

### Description
Usually used if full pivoting on the compromised host is not intended or is having issues

Make sure to replace "X.X.X"
```batch
FOR /L %i IN (1,1,254) DO ping -n 1 X.X.X.%i | FIND /i "Reply" >> c:\ipaddresses.txt
```

