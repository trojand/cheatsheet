# Get Chromium to Proxy traffic to Burp

This may also work on Google Chrome

```
chromium --proxy-server="http://localhost:8080"
# Next step is to import the certificate by going to:
# Settings -> Security -> Manage Certificates -> Authorities
```
    
[^1]: [The Chromium Projects](http://dev.chromium.org/developers/design-documents/network-settings)
