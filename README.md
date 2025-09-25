<img src="https://github.com/user-attachments/assets/851807f6-ed57-475c-a22f-5936f7c7629a" width="1000"><br>
<br>
# LAMP Stack Deployment with osTicket Helpdesk System

### Objective
Deploy and configure osTicket on a Linux virtual machine using the LAMP stack (Linux, Apache, MariaDB, PHP). This project included end-to-end setup of the web server, database, PHP extensions, SSL certificates, firewall rules, and application hardening to deliver a secure and functional ticketing system.


### Skills Learned
- Installing and configuring the LAMP stack on Ubuntu Server
- Setting up and securing OpenSSH for remote server management
- Creating and managing MariaDB databases and user accounts
- Deploying web applications (osTicket) to Apache webroot
- Configuring SSL/TLS certificates and Apache virtual hosts
- Managing Linux file permissions and directory ownership
- Implementing firewall rules for SSH and HTTPS access
- Post-installation hardening of a production web application<br>
  <br>
### Tools Used
- Ubuntu Server 24.04 LTS – Operating system for deployment
- Apache2 – Web server for hosting osTicket
- MariaDB – Relational database for ticketing data
- PHP + Extensions – Application runtime and dependencies
- OpenSSH – Secure shell access for remote management
- UFW (Uncomplicated Firewall) – Firewall rule management
- OpenSSL – SSL/TLS certificate generation
- osTicket v1.18.2 – Open-source ticketing system application

<hr style="border: 0.15px solid rgba(0, 0, 0, 0.05);">
  
### Step 1: Check Network Interface

Verify the IP address assigned to your Linux VM. This ensures the server is reachable on your network before proceeding with package installations and remote configurations.<br>
<br>
- Run ip addr show to display network interfaces and assigned IP addresses.
- Confirm the server has an active network connection.
- Identify the IP (e.g., 192.168.1.161) for use in SSH and web access.<br>

<br>

<hr style="border: 0.15px solid rgba(0, 0, 0, 0.05);">

### Step 2: Install and Configure SSH

Secure Shell (SSH) allows remote access to the Linux server. We install, configure, and validate SSH service to ensure administrators can manage the server remotely.<br>
<br>
- Install OpenSSH server with sudo apt install openssh-server -y.
- Provides secure remote login for system administration.
- Start and enable SSH, configure authentication options, and test connectivity.<br>

<hr style="border: 0.15px solid rgba(0, 0, 0, 0.05);">

### Step 3: Configure Firewall for SSH
Set firewall rules to allow SSH traffic on port 22 and verify it is listening. This prevents accidental lockouts while securing access.<br>
<br>
- Use ufw allow ssh and confirm with ufw status.
- Ensure SSH traffic is permitted through the firewall.
- Validate port 22 is listening with sudo ss -tulpn | grep ssh.<br>
<br>

<hr style="border: 0.15px solid rgba(0, 0, 0, 0.05);">

### Step 4: Install LAMP Stack and PHP Extensions
Deploy Apache, MariaDB, and PHP along with required extensions for osTicket. This forms the foundation of the ticketing system environment.

- Install Apache2, MariaDB, and PHP with modules like php-mysql, php-gd, php-xml.
- osTicket requires LAMP stack and specific PHP extensions for functionality.
- Enable and start Apache and MariaDB services.
<br>
<img src="https://github.com/user-attachments/assets/b1723856-233e-457a-9735-59a5bb2ae670" width="1000"><br>
<br>
<img src="https://github.com/user-attachments/assets/5216bee5-de07-4068-b007-9a37a6156e88" width="1000"><br>
<br>
<img src="https://github.com/user-attachments/assets/80bd2d52-dfd2-49e1-9557-d3171e7badae" width="1000"><br>
<br>
<hr style="border: 0.15px solid rgba(0, 0, 0, 0.05);">

### Step 5: Configure MariaDB Database for osTicket
Create a dedicated database and user account for osTicket with secure credentials generated via OpenSSL.<br>
<br>
- Database name osticket, user ostuser, password stored securely in /root/osticket_db_password.txt.
- Isolate application data and enforce least privilege access.
- Use CREATE DATABASE, CREATE USER, and GRANT ALL PRIVILEGES.<br>
<br>
<img src="https://github.com/user-attachments/assets/e71cba4c-0f99-4442-b2fa-16f75589ce8c" width="1000"><br>

<hr style="border: 0.15px solid rgba(0, 0, 0, 0.05);">

### Step 6: Deploy osTicket and Configure SSL
Download and deploy the osTicket application into the Apache webroot, then configure a self-signed SSL certificate to secure web traffic. This ensures the ticketing system is installed correctly and encrypted for secure user access.

- Download osTicket v1.18.2, move files to /var/www/osticket, and configure file permissions.
- Provides the core helpdesk application while securing connections with SSL/TLS.
- Use wget to fetch the release, configure permissions, and generate SSL certificates with OpenSSL for HTTPS access.<br>
<br>
<img src="https://github.com/user-attachments/assets/4a5eb8f2-41d5-421d-8f35-9e6105432887" width="1000"><br>
<br>
<img src="https://github.com/user-attachments/assets/4358404a-0d6c-4c82-a300-1e071ce95cf9" width="1000"><br>
<br>
<img src="https://github.com/user-attachments/assets/75a22e04-641e-4421-9f2d-cb9f0e7f35e4" width="1000"><br>

<hr style="border: 0.15px solid rgba(0, 0, 0, 0.05);">

### Step 7: Configure Apache Virtual Hosts

Set up Apache virtual hosts for HTTP (port 80) with redirection to HTTPS (port 443). Configure SSL-enabled site for osTicket with security headers.<br>
<br>
- Create /etc/apache2/sites-available/osticket.conf and osticket-ssl.conf.
- Separate osTicket configuration and enforce HTTPS.
- Enable sites with a2ensite, disable defaults, reload Apache.<br>
<br>
<img src="https://github.com/user-attachments/assets/9736ccd8-4b70-4835-9bc3-04388457ed14" width="1000"><br>
<br>
<img src="https://github.com/user-attachments/assets/752b17ad-13ef-42a0-821e-326b13a198fa" width="1000"><br>

<hr style="border: 0.15px solid rgba(0, 0, 0, 0.05);">

### Step 8: Firewall Rules for Web Access

Open necessary firewall ports for HTTP/HTTPS to allow clients to access osTicket via a browser.<br>
<br>
- ufw allow "Apache Secure" (443) and confirm rules.
- Ensure secure access for users to osTicket portal.
- Validate open ports with ss -tulpn.<br>
<br>
<img src="https://github.com/user-attachments/assets/5e283ee2-b237-4323-9252-de2c91db94c4" width="1000"><br>

<hr style="border: 0.15px solid rgba(0, 0, 0, 0.05);">

### Step 9: Verify Installation and Access Installer

Confirm Apache is serving osTicket and verify URLs are reachable over HTTPS.<br>
<br>
- Run curl -kI https://<server-ip>/.
- Check service response before accessing installer.
- Navigate to https://192.168.1.161/setup/install.php.<br>
<br>
<img src="https://github.com/user-attachments/assets/5e84573f-0f0b-4a49-b9a3-a1c8309936ed" width="1000"><br>
<br>
<img src="https://github.com/user-attachments/assets/e416849b-7988-4bee-b6a2-b59def536fa9" width="1000"><br>

<hr style="border: 0.15px solid rgba(0, 0, 0, 0.05);">

### Step 10: Complete Web Installer and Secure osTicket

Run the osTicket web installer in browser, input database credentials, and finalize configuration. After installation, remove setup files and harden permissions.<br>
<br>
- Delete /var/www/osticket/setup and reset permissions on ost-config.php.
- Prevent unauthorized access or reinstallation attempts.
- Restrict file permissions and assign ownership back to www-data.<br>
<br>
<img src="https://github.com/user-attachments/assets/3eef067b-0b05-4a1f-a317-136ec367d324" width="1000"><br>
<br>
<img src="https://github.com/user-attachments/assets/2dc19fa4-83f6-45f3-802e-b92e0f2076f2" width="1000"><br>

<hr style="border: 0.15px solid rgba(0, 0, 0, 0.05);">


**Keywords:** 
#Ubuntu #UbuntuServer #LAMP #Apache #MariaDB #PHP #osTicket #Helpdesk #TicketingSystem #OpenSSL #SSLCertificate #Firewall #UFW #VirtualHosts #LinuxServer #ServerAdministration #SSH #RemoteAccess #IdentityManagement #WebDeployment #NetworkSecurity #SystemHardening


