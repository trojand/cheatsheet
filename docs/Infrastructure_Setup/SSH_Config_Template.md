# SSH Config Template

From Linuxize[^1]

1. Edit your `#!bash ~/.ssh/config`

    ```bash
        Host web_server_1
                HostName 20x.12x.32.xxx
                User iamauser
                Port 2222
                IdentityFile ~/.ssh/id_ed25519
                LogLevel INFO
                Compression yes
                VisualHostKey=yes
    ```

1. Command to connect: `#!bash ssh web_server_1`


[^1]: [Linuxize](https://linuxize.com/post/using-the-ssh-config-file/)