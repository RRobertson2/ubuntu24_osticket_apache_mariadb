
### 2025-09-14 19:07:24



### 2025-09-14 19:08:34

```bash
sudo apachectl configtest
```

### 2025-09-14 19:14:33

```bash
$ sudo apachectl configtest
sudo: apachectl: command not found
```

### 2025-09-15 11:16:20 — VM setup

```bash
sudo apt install -y apache2 mariadb-server
sudo apt install -y libapache2-mod-php php php-mysql php-gd php-mbstring php-imap php-xml php-intl php-curl php-zip php-apcu
php -v
```
php -v
PHP 8.3.6 (cli) (built: Jul 14 2025 18:30:55) (NTS)
Copyright (c) The PHP Group
Zend Engine v4.3.6, Copyright (c) Zend Technologies
    with Zend OPcache v8.3.6, Copyright (c), by Zend Technologies

php -m | egrep 'mbstring|mysqli|gd|intl|imap|curl|zip|apcu'
apcu
curl
gd
imap
intl
mbstring
mysqli
zip

sudo systemctl reload apache2

sudo systemctl enable --now apache2
Synchronizing state of apache2.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable apache2
