# Passive Mode - Kali

Set Kali interface to Passive mode to avoid spooking the NAC and other sensors

Commonly used for initial network monitoring which later may provide understanding of the current network the device is in (Devices within the network, MAC address to copy/pattern on, NAC address, etc)

```bash
sudo systemctl stop NetworkManager.service
sudo systemctl disable NetworkManager.serivce

sudo ifconfig eth0 down
sudo ifconfig eth0 hw ether 00:00:00:00:00:01 promisc #Not sure setting the mac address helps
sudo ifconfig eth0 -arp

sudo route -nv
sudo netstat -r

sudo ip link set eth0 multicast off
sudo ip link set eth0 promisc on
sudo ip link set eth0 up
```