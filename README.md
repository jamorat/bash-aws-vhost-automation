# Bash script(s) for easy, shared tenancy webhosting setup on Amazon AWS (AMI Linux)
<hr>
Bash script(s) for Amazon Linux AMI that automatically set up webhosting (based on user input in the terminal) as follows:
   
   * New virtual hosts by creating new nodes in the config file (vhost.conf)<br>
   * New SFTP-only user and website document root (in ~user/public_html)<br>
   * SSH keypair for new user<br>
   * Chrooted SFTP home directory (~user)<br>
   
Automates a common task, reduces the need to edit multiple files, reduces key strokes, and produces a uniformly configured multi-tenant webhosting environment.


## Assumptions
* EC2 Instance running Amazon Linux AMI (CentOS may work as well)<br>
* httpd and dependencies installed through yum<br>
* Updated default home directory permissions to 755 (in /etc/login.defs)<br>
* Modified sftp subsystem in /etc/ssh/sshd_config to internal-sftp<br>


## Installation
>sudo wget https://raw.githubusercontent.com/neil-sabol/bash-aws-vhost-sftp-chroot-automation/master/make-vhost -O /usr/bin/make-vhost<br>
>sudo chmod 755 /usr/bin/make-vhost<br>


## Usage
>sudo /usr/bin/make-vhost<br>

Scripts ensures dependencies are met, collects your input for domain name (URL) and username (associated with the new site's document root), and generates/edits configuration files accordingly.


## How It Works
### Pre-flight
Checks for root access and presence of httpd (installed through yum/rpm)
Checks for the presence of internal-sftp subsystem in /etc/ssh/sshd_config
### Execution
Prompts for domain name and username for the new site<br>
Checks /etc/httpd/conf.d to see if vhost.conf exists (creates it if not)<br>
Creates new user, along with public_html document root under their home directory<br>
Appends new node to vhost.conf<br>
Appends new block to sshd_config (for chrooted sftp)<br>
SSH keypair is generated for new user and ec2-user's authorized_keys are copied to new user<br>
(optionally, private key printed to screen for local copy/paste, if desired)<br>
### Clean up
Prompts for httpd and sshd reload<br>
Displays summary of operation<br>
