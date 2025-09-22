### osTicket Final Setup

```bash
# Show server IP
ip addr show

# Install SSH
sudo apt install openssh-server -y

# Check SSH status
sudo systemctl status ssh

# Enable SSH
sudo systemctl start ssh

# Firewall rules
sudo ufw allow ssh
sudo ufw status

# Verify Port 22 listening
sudo ss -tulpn | grep ssh

# SSH config
sudo mkdir -p /etc/ssh/sshd_config.d
sudo nano /etc/ssh/sshd_config.d/99-local.conf
# Add the following lines:
# PasswordAuthentication yes
# PubkeyAuthentication yes
# KbdInteractiveAuthentication yes
# ChallengeResponseAuthentication no
# UsePAM yes

# Restart SSH
sudo systemctl restart ssh

# Test SSH connection
ssh -vv osticket@192.168.1.161

#!/usr/bin/env bash
set -e

# 1. LAMP and PHP extensions
sudo apt update
sudo apt install -y apache2 mariadb-server
sudo apt install -y libapache2-mod-php php php-mysql php-gd php-mbstring php-imap php-xml php-intl php-curl php-zip php-apcu unzip git curl

# 2. Enable services and Apache modules
sudo systemctl enable --now apache2
sudo systemctl enable --now mariadb
sudo a2enmod ssl rewrite headers

# 3. Generate DB password and save it
command -v openssl >/dev/null || sudo apt install -y openssl
OSTPASS="$(openssl rand -base64 24)"
echo "osTicket DB password: $OSTPASS"
echo "$OSTPASS" | sudo tee /root/osticket_db_password.txt >/dev/null
sudo chmod 600 /root/osticket_db_password.txt

# 4. Create database and user
sudo mysql -e "
CREATE DATABASE osticket CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'ostuser'@'localhost' IDENTIFIED BY '${OSTPASS}';
GRANT ALL PRIVILEGES ON osticket.* TO 'ostuser'@'localhost';
FLUSH PRIVILEGES;"

# Optional verification
sudo mysql -e "SHOW CREATE DATABASE osticket\G"
sudo mysql -e "SELECT @@character_set_server AS server_charset, @@collation_server AS server_collation;"

# 5. Download osTicket and place into one webroot
cd /tmp
wget https://github.com/osTicket/osTicket/releases/download/v1.18.2/osTicket-v1.18.2.zip
unzip -o osTicket-v1.18.2.zip

sudo mkdir -p /var/www/osticket
sudo cp -r upload/* /var/www/osticket/

# 6. Prepare config for installer
sudo cp /var/www/osticket/include/ost-sampleconfig.php /var/www/osticket/include/ost-config.php
sudo chmod 666 /var/www/osticket/include/ost-config.php

# Ownership and permissions
sudo chown -R www-data:www-data /var/www/osticket
sudo find /var/www/osticket -type d -exec chmod 755 {} \;
sudo find /var/www/osticket -type f -exec chmod 644 {} \;

# 7. Self signed certificate
sudo openssl req -x509 -newkey rsa:4096 -sha256 -days 1095 -nodes \
  -keyout /etc/ssl/private/osticket.key \
  -out /etc/ssl/certs/osticket.crt \
  -subj '/CN=osticketlab' \
  -addext 'subjectAltName=DNS:osticketlab'

sudo chmod 600 /etc/ssl/private/osticket.key
sudo chmod 644 /etc/ssl/certs/osticket.crt
sudo chown root:root /etc/ssl/private/osticket.key /etc/ssl/certs/osticket.crt

# 8. Apache vhosts and ServerName
echo "ServerName osticketlab" | sudo tee /etc/apache2/conf-available/servername.conf >/dev/null
sudo a2enconf servername

# HTTP vhost
sudo tee /etc/apache2/sites-available/osticket.conf >/dev/null <<'EOF'
<VirtualHost *:80>
    ServerName osticketlab
    DocumentRoot /var/www/osticket
    <Directory /var/www/osticket>
        AllowOverride All
        Require all granted
        Options -Indexes
    </Directory>
    RewriteEngine On
    RewriteCond %{HTTPS} off
    RewriteRule ^/(.*)$ https://%{HTTP_HOST}/$1 [R=301,L]
</VirtualHost>
EOF

# HTTPS vhost
sudo tee /etc/apache2/sites-available/osticket-ssl.conf >/dev/null <<'EOF'
<IfModule mod_ssl.c>
<VirtualHost *:443>
    ServerName osticketlab
    DocumentRoot /var/www/osticket

    <Directory /var/www/osticket>
        AllowOverride All
        Require all granted
        Options -Indexes
    </Directory>

    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/osticket.crt
    SSLCertificateKeyFile /etc/ssl/private/osticket.key

    Header always set X-Content-Type-Options "nosniff"
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-XSS-Protection "1; mode=block"
</VirtualHost>
</IfModule>
EOF

# Enable your sites and disable default
sudo a2ensite osticket.conf osticket-ssl.conf
sudo a2dissite 000-default.conf

# 9. Firewall
sudo ufw allow OpenSSH
sudo ufw allow "Apache Secure"

# 10. Validate and reload
sudo apachectl configtest
sudo systemctl reload apache2

# 11. Quick check
sudo ss -tulpn | egrep ':(80|443)\s' || true
curl -kI https://192.168.1.161/ || true

# 12. Post install hardening
sudo rm -rf /var/www/osticket/setup
sudo chmod 644 /var/www/osticket/include/ost-config.php
sudo chown -R www-data:www-data /var/www/osticket
