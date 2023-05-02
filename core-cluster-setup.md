## Detailed setup script for Core Cluster (MAIN VPS)
Target: VM.Standard.E2.1.Micro
<br>
Image: Canonical-Ubuntu-22.04-Minimal-2023.02.14-0
<br>
<br>

Remember to run below commands with **sudo** permission:

1. Follow `basic-setup.md` document.
2. `[OPTIONAL]` Install swap memory (2 GB) if limited on RAM:
```sh
fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
cp /etc/fstab /etc/fstab.bak
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```
Paste at the end those caching properties: `nano /etc/sysctl.conf`
```sh
vm.swappiness=20
vm.vfs_cache_pressure=50
```
3. Install Nginx: `apt install nginx`
4. Add port 80 into iptables: `nano /etc/iptables/rules.v4`
```sh
-A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
```
5. Replace nginx config with `nginx.conf` file: `nano /etc/nginx/sites-available/default`