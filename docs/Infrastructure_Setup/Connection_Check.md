# Connection check to VPN or network

## Description

Simple way that can be done in most Linux OS (ICMP)

Uses **nping** (from nmap) for TCP Ping

To know if you have:

   * been blocked by WAF, IPS, NAC
   * been disconnected from your VPN (i.e. ovpn)
   * oversaturated your connection (Lessen your threads)
   * TCP Ping for second verification or if host does not respond to ICMP

Make sure to change:

   * $hostORdomain
   * $destPort

## Bash
```bash
cd ~
echo 'alias con_check_icmp="ping $hostORdomain |cut -d \"=\" -f 2,4"' >> .bashrc
echo "alias con_check_tcp=\"sudo nping --tcp --delay 1s -c 0 -H -p $destPort $hostORdomain | awk '/mss/ {print \\\$7,\\\$10,\\\$13,\\\$14}'\"" >> .bashrc
source .bashrc
```

## ZSH
```bash
cd ~
echo 'alias con_check_icmp="ping $hostORdomain |cut -d \"=\" -f 2,4"' >> .zshrc
echo "alias con_check_tcp=\"sudo nping --tcp --delay 1s -c 0 -H -p $destPort $hostORdomain | awk '/mss/ {print \\\$7,\\\$10,\\\$13,\\\$14}'\"" >> .zshrc
source .zshrc
```

## To Execute
```bash
# For ICMP
con_check_icmp
# For TCP
con_check_tcp
# On terminator "Watch for silence"
```
## Tip

* I use Terminator and its plugin "InactivityWatch" to watch if it stops pinging
