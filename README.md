bash-aws-web-hosting-automation
=========================

Bash script for Amazon Linux AMI that automatically sets up webhosting (based on user input in the terminal) as follows:
   
   New virtual hosts by creating new nodes in the config file that are<br>
   New SFTP user and website document root (in ~user/public_html)<br>
   SSH keypair for new user<br>
   Chrooted SFTP home directory (~user)<br>
   
Automates common tasks, reduces the need to edit files, reduces key strokes.


#Assumptions
Using Amazon Linux AMI<br>
Installed httpd24 through yum<br>
Updated default home directory permissions to 755 (in /etc/login.defs)<br>
Modified sftp subsystem in /etc/ssh/sshd_config to internal-sftp<br>


#Installation
Place the "make-vhost" bash script into /usr/bin<br>
chmod 755<br>
sudo make-vhost<br>


#How It Works
Script checks /etc/httpd/conf.d to see if vhost.conf exists<br>
Script creates vhost.conf if it does not already exist<br>
Script prompts for domain name and username for the new site<br>
New user is created, along with public_html document root under their home directory<br>
A new node is appended to end of vhost.conf<br>
A new block is appended to the end of sshd_config (for chrooted sftp)<br>
SSH keypair is generated for new user (optionally, private key printed to screen for local copy/paste, if desired)<br>
Script prompts for httpd and sshd reload<br>
All done!<br>
