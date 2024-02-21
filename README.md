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

        sudo apt update && sudo apt upgrade


<a name="step-4-install-ispconfig-with-nginx-web-server"></a>
## Step 4: Install ISPConfig with Nginx Web Server
The auto-installer below contains the following software: **PHP**, **MariaDB**, **Postfix**, **Dovecot**, **Rspamd**, **BIND**, **Jailkit**, **Roundcube**, **PHPMyAdmin**, **Mailman**, **Webalizer**, **AWStats** and **GoAccess**.
```
    wget -O - https://get.ispconfig.org | sh -s -- --use-nginx --use-ftp-ports=40110-40210 --unattended-upgrades
```
> We can easily choose not to use certain functions or to install additional services by passing arguments to the installer
to install additional services ```--use-``` to choose not to use certain functions ```--no-```. 
<a name="step-5-configure-ispconfig"></a>
## Step 5: Configure ISPConfig
*Note: Your ISPConfig and MySQL root passwords will be displayed.*
1. Connect to ISPConfig: `https://server1.example.com:8080/login/`.
2. Create a client with provided details.
3. Set up Web Domain `test.server1.example.com` and configure HTTP to HTTPS redirection.
<a name="step-6-setting-up-the-firewall"></a>
## Step 6: Setting Up the Firewall
**Configure the firewall to allow ports:** `20, 21, 22, 25, 80, 443, 40110-40210, 110, 143, 465, 587, 993, 995, 53, 8080, 8081`.
<a name="step-7-install-dependencies-for-odoo"></a>
## Step 7: Install Dependencies for Odoo
    ```
    sudo apt install python3 python3-dev python3-pip python3-venv python3-setuptools build-essential libzip-dev libxslt1-dev libldap2-dev python3-wheel libsasl2-dev node-less libjpeg-dev xfonts-75dpi xfonts-base libpq-dev libffi-dev fontconfig git wget nodejs npm
    ```
<a name="step-8-install-and-configure-postgresql"></a>
## Step 8: Install and Configure PostgreSQL
1. Install PostgreSQL
    ```
    sudo apt-get install postgresql-15
    sudo systemctl start postgresql
    sudo systemctl enable postgresql
    ```
2. Create a PostgreSQL user for Odoo
    ```
    sudo su - postgres -c "createuser -s odoo"
    ```
<a name="step-9-install-nodejs-and-wkhtmltopdf"></a>
## Step 9: Install Node.js and wkhtmltopdf
1. Install Node.js and npm
    ```
    sudo apt install nodejs npm
    sudo npm install -g rtlcss
    ```
2. Install wkhtmltopdf
    ```
    wget https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6.1-3/wkhtmltox_0.12.6.1-3.bookworm_amd64.deb
    sudo dpkg -i wkhtmltox_0.12.6.1-3.bookworm_amd64.deb
    ```
<a name="step-10-create-an-odoo-user-and-install-odoo"></a>
## Step 10: Create an Odoo User and Install Odoo
1. Create a user for Odoo
    ```
    sudo adduser --system --group --home=/opt/odoo --shell=/bin/bash odoo
    sudo su - odoo
    ```
2. Clone the Odoo repository and install dependencies:
    ```
    git clone https://www.github.com/odoo/odoo --depth 1 --branch 17.0 /opt/odoo/odoo
    python3 -m venv odoo-env
    source odoo-env/bin/activate
    pip3 install wheel
    pip3 install -r odoo/requirements.txt
    deactivate
    ```
3. Create a directory for custom addons and set permissions:
    ```
    mkdir /opt/odoo/custom-addons
    exit
    sudo mkdir /var/log/odoo
    sudo chown odoo:odoo /var/log/odoo
    ```
<a name="step-11-configure-odoo"></a>
## Step 11: Configure Odoo
1. Create and edit `/etc/odoo.conf` to add the Odoo configuration.
   ```
   nano /etc/odoo.conf
   ```
   ```
   [options]
    admin_passwd = Strong_admin_Password
    db_host = False
    db_port = False
    db_user = odoo
    db_password = False
    logfile = /var/log/odoo/odoo-server.log
    addons_path = /opt/odoo/odoo/addons,/opt/odoo/custom-addons
    xmlrpc_port = 8069
   ```
3. Create and edit `/etc/systemd/system/odoo.service` for systemd service configuration.
   ```
    nano /etc/systemd/system/odoo.service
   ```
   ```
   [Unit]
    Description=Odoo
    Requires=postgresql.service
    After=network.target postgresql.service
    
    [Service]
    Type=simple
    SyslogIdentifier=odoo
    PermissionsStartOnly=true
    User=odoo
    Group=odoo
    ExecStart=/opt/odoo/odoo-env/bin/python3 /opt/odoo/odoo/odoo-bin -c /etc/odoo.conf
    StandardOutput=journal+console
    
    [Install]
    WantedBy=multi-user.target
    ```
   
5. Enable and start the Odoo service:
   ```
    sudo systemctl daemon-reload
    sudo systemctl start odoo
    sudo systemctl enable odoo
   ```
<a name="step-12-configure-reverse-proxy"></a>
## Step 12: Configure Reverse Proxy
1. Edit the Nginx configuration file `/etc/nginx/sites-available/test.server1.example.com` and configure the reverse proxy settings.
    ```
    nano /etc/nginx/sites-available/test.server1.example.com
    ```
add this following configuration to the server

    ```
        location / {
                proxy_pass http://localhost:8069/;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto $scheme;
                
                # CORS headers
                add_header 'Access-Control-Allow-Origin' '*' always;
                add_header 'Access-Control-Max-Age' '300' always;
                add_header 'Access-Control-Allow-Headers' 'x-requested-with, Content-Type, Access-Token, access-token, origin, authorization, accept, client-security-token' always;
                add_header 'Access-Control-Allow-Methods' 'GET, POST, PUT, DELETE, OPTIONS, HEAD, CONNECT, TRACE, PATCH' always;
    
                # Cache Control
                add_header 'Cache-Control' 'max-age=300, no-cache' always;
        }
    
    ```

---

*End of Installation Guide*
