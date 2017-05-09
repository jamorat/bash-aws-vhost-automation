# Bash script(s) for easy, shared tenancy webhosting setup on Amazon AWS (AMI Linux)

Bash script(s) for Amazon Linux AMI that automatically set up basic web hosting (based on user input in the terminal) as follows:
   
   * (make-vhost) New virtual hosts by creating new nodes in the config file (vhost.conf)<br>
   * (make-vhost) New SFTP-only user and website document root (in ~user/public_html)<br>
   * (make-vhost) SSH keypair for new user<br>
   * (make-vhost) Chrooted SFTP home directory (~user)<br>
   * (make-vhost-ssl) SSL (https) certificate install (via @Neilpang's [acme.sh](https://github.com/Neilpang/acme.sh)) and SSL vhost creation<br>
   
Automates common tasks, reduces the need to edit multiple files, reduces key strokes, and produces a relatively secure, uniformly configured multi-tenant webhosting environment.


## Installation
>sudo wget https://raw.githubusercontent.com/neil-sabol/bash-aws-vhost-sftp-chroot-automation/master/make-vhost -O /usr/bin/make-vhost<br><br>
>sudo wget https://raw.githubusercontent.com/neil-sabol/bash-aws-vhost-sftp-chroot-automation/master/make-vhost-ssl -O /usr/bin/make-vhost-ssl<br><br>
>sudo chmod 755 /usr/bin/make-vhost*<br>


## Usage
>sudo make-vhost<br>
>sudo make-vhost-ssl<br>

Scripts ensure dependencies are met, collect your input for domain name (URL) and username (associated with the new site's document root), and generate/edit configuration files to procure certificates accordingly.

For more detailed information about what tasks these scripts automate, read below.


## Assumptions and Requirements
### make-vhost
* Updated default home directory permissions to 755 (in /etc/login.defs)<br>
```
vi /etc/login.defs
REPLACE UMASK 077 with UMASK 022
```
* Modified sftp subsystem in /etc/ssh/sshd_config to internal-sftp<br>
```
vi /etc/ssh/sshd_config
REPLACE "Subsystem sftp /usr/libexec/openssh/sftp-server" with "Subsystem sftp internal-sftp"
```

### make-vhost-ssl
* git installed and functional
```
yum install -y git
```
* mod_ssl installed and functional
```
yum install -y mod24_ssl
```

### both
* EC2 Instance running Amazon Linux AMI (CentOS/RHEL may work as well)<br>
* httpd and dependencies installed through yum<br>
```
yum install -y httpd24
```
* Scripts executed as root (via sudo)


## More explicit explanation of what the scripts do:

### make-vhost
1. Prerequesite checks (run as root, presence of httpd, sshd_config)
2. Prompt for new domain and username
3. Create file /etc/httpd/conf.d/vhost.conf if it does not already exist
4. Create user and DocumentRoot (~user/public_html) and set permissions (2750) and ownership (user:apache)
5. Add new configuration section (http/80 vhost) to /etc/httpd/conf.d/vhost.conf
```
<VirtualHost *:80>
ServerName my.new.url
DocumentRoot /home/user/public_html
# Add other directives below this line
</VirtualHost>
```
6. Add new configuration section to /etc/ssh/sshd_config
```
Match User user
ChrootDirectory /home/user
ForceCommand internal-sftp
AllowTCPForwarding no
X11Forwarding no
```
7. Create 2048 bit RSA keypair for new user (~user/.ssh/id_rsa and id_rsa.pub)
8. Add ec2-user's public key (~/ec2-user/.ssh/authorized_keys) to user's account (~user/.ssh/authorized_keys)
9. Prompt for service (httpd and sshd) reload

### make-vhost-ssl
1. Prerequisite checks (run as root, presence of httpd/mod_ssl, sshd_config, Neil Pang's acme.sh)
2. Installs acme.sh if not found (installs for root, certificate directory /etc/letsencrypt)
```
acme.sh --install --certhome /etc/letsencrypt
```
3. Prompt for new domain based on existing (http/port 80) vhosts in /etc/httpd/conf.d/vhost.conf
4. Process selected vhost, extract document root, ensure it exists, and prompt for correctness
5. Run acme.sh to procure certificates for the domain from Let's Encrypt
```
/root/.acme.sh/acme.sh.env
/root/.acme.sh/acme.sh --issue -d $domainname -w $documentroot
```
6. Add new configuration section (https/443 vhost) to /etc/httpd/conf.d/vhost.conf
```
<VirtualHost *:443>
ServerName my.new.url
DocumentRoot /home/user/public_html
SSLEngine on
SSLCertificateFile /etc/letsencrypt/my.new.url/fullchain.cer
SSLCertificateKeyFile /etc/letsencrypt/my.new.url/my.new.url.key
# Add other directives below this line
</VirtualHost>
```
7. Prompt for service (httpd and sshd) reload
