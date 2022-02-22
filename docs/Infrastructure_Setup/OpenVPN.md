# OpenVPN Tips

## Make OpenVPN automatically connect upon boot

Make sure to change the `OPENVPN_CONFIGFILE` variable

```bash
sudo cp OPENVPN_CONFIGFILE.ovpn /etc/openvpn/OPENVPN_CONFIGFILE.conf
sudo systemctl enable openvpn
```

---
## Saving OpenVPN Credentials
* So you wont need to type it every time you connect
* For Username password
    * Within the 'OS-90922.ovpn', add the path to you text file containing the openvpn credentials:
        ```
        auth-user-pass /home/kali/VPN/creds.conf
        ```
    * Simple username the password for the contents of creds.conf. Top is the username, bottom is the password. (i.e. Offensive Security VPN):
        ```
        OS-90922
        kASme0eHLeI3
        ```
* For private key
    * Within the 'OS-90922.ovpn', add the path to you text file containing the openvpn credentials:
        ```
        askpass /home/kali/VPN/private_key.conf
        ```
    * Example contents of /home/kali/VPN/private_key.conf
        ```
        kASme0eHLeI3
        ```
