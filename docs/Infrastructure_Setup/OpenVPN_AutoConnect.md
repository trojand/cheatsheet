# Make OpenVPN automatically connect upon boot

Make sure to change the `OPENVPN_CONFIGFILE` variable

```bash
sudo cp OPENVPN_CONFIGFILE.ovpn /etc/openvpn/OPENVPN_CONFIGFILE.conf
sudo systemctl enable openvpn
```