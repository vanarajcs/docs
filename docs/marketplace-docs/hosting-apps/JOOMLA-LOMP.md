﻿---
title: JOOMLA-LOMP(UBUNTU-20.04 TLS)
sidebar_label: JOOMLA-LOMP
---

**Description:**

OpenLiteSpeed is the Open Source edition of LiteSpeed Web Server Enterprise and contains all of the essential features, including HTTP/3 support. OLS provides enormous scalability and an accelerated hosting platform for Joomla. This application gives you OpenLiteSpeed, PHP, MySQL Server, Joomla, and other useful applications.

**Software Included:**

Joomla - 4.2.2

OpenLiteSpeed - 1.7.16

MariaDB - 10.3.37

LSPHP - 8.0

Postfix - 3.4.13

Certbot - 0.40.0

Phpmyadmin - 5.2.0

**Getting started after deploying Joomla:**

 Allow the ports in the firewall only SSH (port 22, rate limited), HTTP (port 80), HTTPS (port 443) access, and Litespeed Admin Panel (port 7080).

 Runs mysql_secure_installation, and creates a Joomla user with the necessary permissions, sets the OpenLitespeed Admin Dashboard Password.

 Creates the initial Joomla configuration file and allows the Joomla instance to connect to the database.

 You will be logged out of the instance and displayed with the below message until the instance is deployed. **DO NOT LOG IN TO THE INSTANCE FOR 2 MINUTES AFTER THE CREATION.**
~~~
Please wait until the installation is completed....
Connection to $IPADDRESS closed.
~~~

**Once the Joomla is deployed:**

 You can log into the instance as ubuntu using either the password you set when you created the instance or with an SSH key if you added one during creation.

 You can then switch the user to root without a password by entering the following command. **< sudo su - >**

 When you connect to your Joomla Instance via SSH for the 1st time, you’ll be displayed with the passwords and prompted to enter a domain name to complete the setup and secure your connection, you will need a registered domain configured for your Instance.
~~~
To cancel setup, press Ctrl+C.  This script will run again on your next login:
Enter the domain name for your new Joomla site:
(ex. example.org or test.example.org) do not include www or http/s:
Domain/Subdomain name: 
~~~

 Domain's A record must be pointed to the Instance's IPADDRESS.

 Once the setup is completed without any issues, you will be able to access the domain name or Server's IPADDRESS in your browser to finish the Joomla installation through the web interface.

 The MariaDB root password is stored under **/root/.mariadb_root_password**

 Login to MariaDB using the command  **mysql**  and Click Enter

 The user **dbadmin** has the same capabilities as the root account but is configured for password authentication

 The **root** MariaDB user is set to authenticate using the **unix_socket** plugin by default rather than with a password. So setting up password for **root** user is not recommended.

 You can access phpMyAdmin immediately by visiting the Instance’s IP address in your browser followed by **/phpmyadmin**

 You can view the php information immediately by visiting the Instance’s IP address in your browser followed by  **/phpinfo.php**

 Joomla Database Informations are stored under **/root/.joomla_database_details**

 The OpenLitespeed Admin Dashboard Login Credential is stored under  **/root/.Litespeed_Admin_Password**

 Access OpenLitespeed Admin Dashboard through the web using http://IPADDRESS:7080

 The web root is /var/www/html and the Joomla configuration file is **/var/www/html/configuration.php.**

Setting up an SSL certificate enables HTTPS on the web server, which secures the traffic between the server and the clients connecting to it. Certbot is a free and automated way to set up SSL certificates on a server. It’s included as part of the Joomla deployment to make securing the domain easier.

**To use Certbot, you’ll need a registered domain name and two DNS records:**

An A record from a domain (e.g., example.com) to the server’s IP address

An A record from a domain prefaced with www (e.g., www.example.com) to the server’s IP address

Additionally, if you’re using a virtual hosts file, you’ll need to make sure the server name directive in the VirtualHost block (e.g., ServerName example.com) is correctly set to the domain.

Once the DNS records and, optionally, the virtual host files are set up, you can generate the SSL certificate. Make sure to substitute the domain in the command.

~~~
certbot certonly -d example.com -d www.example.com  -w /var/www/html/
~~~

 Once the SSL setup is completed, Follow the below steps for automatic redirection from HTTP to HTTPS

 Open the virtualhost file **/usr/local/lsws/conf/vhosts/joomla/vhconf.conf**

 In the **"rewrite"** section, paste the below code,
~~~
  rules                   <<<END_rules
RewriteCond %{SERVER_PORT} ^80$
RewriteRule .* https://%{SERVER_NAME}%{REQUEST_URI} [R=301,L]
  END_rules
~~~

It must be changed from,
~~~
rewrite  {
  enable                  1
  autoLoadHtaccess        1
  
}
~~~

TO
~~~
rewrite  {
  enable                  1
  autoLoadHtaccess        1
  rules                   <<<END_rules
RewriteCond %{SERVER_PORT} ^80$
RewriteRule .* https://%{SERVER_NAME}%{REQUEST_URI} [R=301,L]
  END_rules

}
~~~

Full restart the litespeed using the below command
~~~
/usr/local/lsws/bin/lswsctrl fullrestart
~~~

Now the domain is automatically redirected from HTTP to HTTPS.

HTTPS traffic on port 443 needs to be allowed through the firewall. After you set up HTTPS, you can optionally deny HTTP traffic on port 80:

**Setting up Postfix:**

 Set a Valid Hostname using the command **"hostnamectl set-hostname test.example.com".** Make sure the hostname "test.example.com" have a proper A record.

 The Instance IP must have a proper PTR record.

 Configure the hostname in the postfix configuration **/etc/postfix/main.cf**

In the section below section, replace the **$hostname** with the valid hostname
~~~
myhostname = $hostname
~~~

 Restart postfix using the command
~~~
systemctl restart postfix
~~~

 Once the above step is completed. You can check outgoing Emails using the command
~~~
echo "Postfix test" | mail -s "Subject" test@gmail.com"
~~~

Initially, the Emails will be dropped into the SPAM folder. As this is a NEW IP the reputation is unknown. Once the reputation is calculated based on the incoming and outgoing emails. The emails will be dropped in the INBOX.
