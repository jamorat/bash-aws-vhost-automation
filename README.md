bash-aws-web-hosting-automation
=========================

Bash script for Amazon Linux AMI that automatically sets up webhosting (based on user input in the terminal) as follows:
   
   New virtual hosts by creating new nodes in the config file<br>
   New SFTP user and website document root (in ~user/public_html)<br>
   SSH keypair for new user<br>
   Chrooted SFTP home directory (~user)<br>
   
Automates common tasks, reduces the need to edit files, reduces key strokes.


<strong>Assumptions</strong>

Using Amazon Linux AMI<br>
Installed httpd24 through yum<br>
Updated default home directory permissions to 755 (in /etc/login.defs)<br>
Modified sftp subsystem in /etc/ssh/sshd_config to internal-sftp<br>


<strong>Installation</strong>

begin with: /usr/bin/make-vhost
sudo chmod 755 /usr/bin/make-vhost
sudo /usr/bin/make-vhost

<strong>How It Works</strong>

Scripts checks for root access and presence of httpd 2.4 (installed through yum/rpm)
Script prompts for domain name and username for the new site<br>
Script checks /etc/httpd/conf.d to see if vhost.conf exists (creates it if not)<br>
New user is created, along with public_html document root under their home directory<br>
A new node is appended to end of vhost.conf<br>
A new block is appended to the end of sshd_config (for chrooted sftp)<br>
SSH keypair is generated for new user and ec2-user's authorized_keys are copied to new user (optionally, private key printed to screen for local copy/paste, if desired)<br>
Script prompts for httpd and sshd reload<br>
Summary of operations is displayed<br>
