## Detailed setup script for Light Cluster (DATABASE VPS)
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
3. Install MySQL: `apt install mysql-server`
4. Configure MySQL with setup script: `mysql_secure_installation`
- Setup **LOW** password requirements,
- `[OPTIONAL]` If it fails, run below commands:
```sql
killall -9 mysql_secure_installation
mysql # Open MySQL client connection

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '[PASSWORD_HERE]';
exit
```
5. Optimize MySQL, edit below config lines: `nano /etc/mysql/mysql.conf.d/mysqld.cnf`
- Edit/Add **[mysqld]** section:
```sh
bind-address            = 10.0.0.63 # Use private ip of set machine
mysqlx-bind-address     = 10.0.0.63

[mysqld]
innodb_buffer_pool_size = 700M # Use 70% of VPS RAM
performance_schema = OFF
symbolic-links = 0
skip-external-locking = 1
key_buffer_size = 32K
max_allowed_packet = 4M
table_open_cache = 8
sort_buffer_size = 128K
read_buffer_size = 512K
read_rnd_buffer_size = 512K
net_buffer_length = 4K
thread_stack = 480K
innodb_file_per_table = 1
max_connections = 50
max_user_connections = 50
wait_timeout = 1800 # 30 minutes
interactive_timeout = 1800 # 30 minutes
```
- Restart database: `systemctl restart mysql`
7. Create `lightcluster` database and proper user: `mysql -u root -p`
```sql
CREATE DATABASE lightcluster;
SET GLOBAL validate_password.policy = 0;
CREATE USER 'kikuri'@'%' IDENTIFIED WITH mysql_native_password BY '[PASSWORD_HERE]';
GRANT ALL PRIVILEGES ON lightcluster.* TO 'kikuri'@'%';
FLUSH PRIVILEGES;
```
8. Reconstruct tables from backup file: `mysql -u root -p lightcluster < /path/to/backup.sql`