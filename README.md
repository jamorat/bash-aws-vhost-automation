bash-aws-vhost-automation
=========================

Bash script for Amazon Linux AMI that automatically sets up new virtual hosts by creating new nodes in the config file that are based on user input in the terminal. Automates a common task, reduces the need to edit files, reduces key strokes.

About the make-virtual-host script<br>
Assumes you are using Amazon Linux AMI<br>
Place this bash script into /usr/bin so you can run it from anywhere<br>
Chmod 755<br>
Script checks /etc/httpd/conf.d to see if vhost.conf exists<br>
Will create vhost.conf if it does not already exist<br>
User is asked for domain name and path to web root of new site<br>
A new node is appended to end of vhost.conf
