﻿---
title: MYSQL-STANDALONE(UBUNTU-22.04 TLS)
sidebar_label: MYSQL-STANDALONE
---

**Description:**

The world's most popular open-source database

**Software Included:**

MYSQL - 8.0

**Getting started after deploying MYSQL:**

Allow the ports in the firewall only SSH (port 22, rate limited) and MySQL (3306) access.

Sets the MySQL root password and runs mysql_secure_installation.

You will be logged out of the instance and displayed with the below message until the instance is deployed.  **DO NOT LOG IN TO THE INSTANCE FOR 2 MINUTES AFTER THE CREATION.**

```
Please wait until the installation is completed....Connection to $IPADDRESS closed.
```

**Once MySQL is deployed:**

You can log into the instance as ubuntu using either the password you set when you created the instance or with an SSH key if you added one during creation.

You can then switch the user to root without a password by entering the following command.  **< sudo su - >**

The MySQL root password is stored under  **/root/.mysql_root_password**

Login to MySQL using the command  **mysql**  and Click Enter
