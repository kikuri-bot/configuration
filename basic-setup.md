## Basic setup script for Oracle's x64 VPS
Target: VM.Standard.E2.1.Micro
<br>
Image: Canonical-Ubuntu-22.04-Minimal-2023.02.14-0
<br>
<br>

Remember to run below commands with **sudo** permission:

1. Update dependencies + install essential packages:
```sh
apt update -y && apt upgrade -y && apt install iputils-ping nano curl unzip -y && apt remove ufw
```
2. Delete snap (release our very previous RAM):
```sh
snap remove oracle-cloud-agent && snap remove core18 && snap remove snapd
```
3. **Replace** port 22 to 7995 in iptables: `nano /etc/iptables/rules.v4`
```sh
❌ -A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT
✔️ -A INPUT -p tcp -m state --state NEW -m tcp --dport 7995 -j ACCEPT
```

3. Change SSH port from default 22 to 7995 and secure connections: `nano /etc/ssh/sshd_config`
4. Make SSH hardening using: https://www.ssh-audit.com/
5. Restart VPS:
```sh
reboot -n
```