# Burp Suite Extensions

## Logger++
* Logger++ Filter[^1]
  * For dumping emails.
  * After this, sort the blob.txt via regex.
      * A small [script](https://github.com/trojand/Script_Yard/blob/main/Blob_to_EmailAddresses.py) is already available to do this on my GitHub
  ```
  Request.Path CONTAINS "/owa/service.svc" AND Request.Query CONTAINS "action=FindPeople&app=People" AND Response.Body CONTAINS "@"
  ```
  
[^1]: [Corey Arthur - Logger Filters](https://coreyarthur.com/logger-filters/)
