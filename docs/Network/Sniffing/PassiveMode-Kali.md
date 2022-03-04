# Passive Mode - Kali

Set Kali interface to Passive mode to avoid spooking the NAC and other sensors

Commonly used for initial network monitoring which later may provide understanding of the current network the device is in (Devices within the network, MAC address to copy/pattern on, NAC address, etc).

Also disables ipv6 [^1]

```bash
sudo systemctl stop NetworkManager.service
sudo systemctl disable NetworkManager.service

ifconfig eth1
sudo route -nv
sudo netstat -r

sudo ifconfig eth1 down
sudo ifconfig eth1 hw ether 00:00:00:00:00:01 promisc #Not sure setting the mac address helps
sudo ifconfig eth1 -arp

ifconfig eth1
sudo route -nv
sudo netstat -r

sudo ip link set eth1 multicast off
sudo ip link set eth1 promisc on
sudo sh -c 'echo 1 > /proc/sys/net/ipv6/conf/eth1/disable_ipv6'
sudo ip link set eth1 up
```

[^1]: [XModulo](https://www.xmodulo.com/disable-ipv6-linux.html)
