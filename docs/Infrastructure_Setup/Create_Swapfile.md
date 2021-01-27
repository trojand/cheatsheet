# Connection check to VPN or network

## Description

Increase swap space by creating a swap file[^1]

## Instructions

```bash
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo cp /etc/fstab /etc/fstab.bak
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

[^1]: [ComputingforGeeks](https://computingforgeeks.com/how-to-create-a-swap-file-on-linux/)
