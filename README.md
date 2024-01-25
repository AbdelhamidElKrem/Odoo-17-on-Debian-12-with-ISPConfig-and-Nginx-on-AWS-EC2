# Installation Guide: Odoo 17 on Debian 12 with ISPConfig and Nginx on AWS EC2

## Table of Contents
1. [AWS EC2 Setup](#step-1-aws-ec2-setup)
2. [Configure the Hostname and Hosts](#step-2-configure-the-hostname-and-hosts)
3. [Update Operating System](#step-3-update-operating-system)
4. [Install ISPConfig with Nginx Web Server](#step-4-install-ispconfig-with-nginx-web-server)
5. [Configure ISPConfig](#step-5-configure-ispconfig)
6. [Setting Up the Firewall](#step-6-setting-up-the-firewall)
7. [Install Dependencies for Odoo](#step-7-install-dependencies-for-odoo)
8. [Install and Configure PostgreSQL](#step-8-install-and-configure-postgresql)
9. [Install Node.js and wkhtmltopdf](#step-9-install-nodejs-and-wkhtmltopdf)
10. [Create an Odoo User and Install Odoo](#step-10-create-an-odoo-user-and-install-odoo)
11. [Configure Odoo](#step-11-configure-odoo)
12. [Configure Reverse Proxy](#step-12-configure-reverse-proxy)
    
<a name="step-1-aws-ec2-setup"></a>
## Step 1: AWS EC2 Setup
  1. Launch a `Debian 12` instance (e.g., t3.small).
  2. Configure `key pair (RSA)`, `storage` (16 GB).
  3. Set up security groups to allow all traffic initially.
  4. Connect via SSH using the provided key pair.
<a name="step-2-configure-the-hostname-and-hosts"></a>
## Step 2: Configure the Hostname and Hosts
> :warning: **Nota Bene:** When following the instructions, be sure to replace `server1.example.com` in the `/etc/hosts` file with your actual server's hostname. This is crucial for the correct network configuration of your server.

1. Edit `/etc/hosts`file :
```
nano /etc/hosts
```
```
127.0.0.1  localhost.localdomain   localhost
# This line should be changed to the correct servername:
127.0.1.1  server1.example.com server1

# The following lines are desirable for IPv6 capable hosts
::1       localhost ip6-localhost ip6-loopback
ff02::1   ip6-allnodes
ff02::2   ip6-allrouters
```

2. Edit `/etc/hostname` file
```
nano /etc/hostname
```
```
server1
```
3. Reboot the server
```
systemctl reboot
```
<a name="step-3-update-operating-system"></a>
## Step 3: Update Operating System
**Disk Space Confirmation:** A message will appear indicating the additional disk space required for the update. Confirm to proceed by typing **`Y`** 
```
sudo apt update && sudo apt upgrade
```


<a name="step-4-install-ispconfig-with-nginx-web-server"></a>
## Step 4: Install ISPConfig with Nginx Web Server
The auto-installer below contains the following software: **PHP**, **MariaDB**, **Postfix**, **Dovecot**, **Rspamd**, **BIND**, **Jailkit**, **Roundcube**, **PHPMyAdmin**, **Mailman**, **Webalizer**, **AWStats** and **GoAccess**.
```
wget -O - https://get.ispconfig.org | sh -s -- --use-nginx --use-ftp-ports=40110-40210 --unattended-upgrades
```
We can easily choose not to use certain functions or to install additional services by passing arguments to the installer
to install additional services ```--use-``` to choose not to use certain functions ```--no-```. 
<a name="step-5-configure-ispconfig"></a>
## Step 5: Configure ISPConfig
...
<a name="step-6-setting-up-the-firewall"></a>
## Step 6: Setting Up the Firewall
...
<a name="step-7-install-dependencies-for-odoo"></a>
## Step 7: Install Dependencies for Odoo
...
<a name="step-8-install-and-configure-postgresql"></a>
## Step 8: Install and Configure PostgreSQL
...
<a name="step-9-install-nodejs-and-wkhtmltopdf"></a>
## Step 9: Install Node.js and wkhtmltopdf
...
<a name="step-10-create-an-odoo-user-and-install-odoo"></a>
## Step 10: Create an Odoo User and Install Odoo
...
<a name="step-11-configure-odoo"></a>
## Step 11: Configure Odoo
...
<a name="step-12-configure-reverse-proxy"></a>
## Step 12: Configure Reverse Proxy
...

---

*End of Installation Guide*
