# Setup OpenSSH Server on a Windows Server

* Used for:
    * Common uses of SSH
* Applies to:
    * Windows Server 2016 before 1709 [^2]
    * Windows Server 2012 and below


### Commands [^1]

1. Download the latest release from the [PowerShell GitHub](https://github.com/PowerShell/Win32-OpenSSH/releases)

1. Extract to a folder like `C:\Program Files\OpenSSH`

1. On an elevated PowerShell terminal
    ```powershell
    powershell -ExecutionPolicy Bypass
    cd "\Program Files\OpenSSH"
    .\install-sshd.ps1
    .\ssh-keygen.exe -A
    New-NetFirewallRule -Protocol TCP -LocalPort 22 -Direction Inbound -Action Allow -DisplayName SSH
    ```

[^1]: [KC's Blog](https://www.kjctech.net/setting-up-sftp-or-ssh-server-on-windows-server-2012-r2/)
[^2]: [NTWeekly](http://www.ntweekly.com/2017/12/22/install-openssh-windows-server-2016-1709/)
