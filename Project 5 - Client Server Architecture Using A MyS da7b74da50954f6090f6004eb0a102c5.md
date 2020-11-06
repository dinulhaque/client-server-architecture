#Client/Server Architecture Using A MySQL Relational Database Management System

# **Understanding Client-Server Architecture**

As I proceed my journey into the world of I.T, I have began to realise that certain concepts apply to many areas, and they will frequently be referred to as Client-Server architecture is one of them.

At its simplest, the client/server architecture is about dividing up application processing into two or more logically distinct pieces. The database makes up half of the client/server architecture. The database is the “server”; any application that uses that data is a “client.” In many cases, the client and server reside on separate machines; in most cases, the client application is some sort of user-friendly interface to the database. Image below provides a graphical representation of a simple client/server system.

![Project%205%20-%20Client%20Server%20Architecture%20Using%20A%20MyS%20da7b74da50954f6090f6004eb0a102c5/Untitled.png](Project%205%20-%20Client%20Server%20Architecture%20Using%20A%20MyS%20da7b74da50954f6090f6004eb0a102c5/Untitled.png)

Client-Server refers to an architecture in which 2 or more computers are connected together over a network to send and receive requests between one another. For simplicity sake, I will focus on just 2 virtual machine/computers for this project.

Generally, the client sends requests to the server and receives requests from the server as well. A **client** can also be your laptop, or the browser on your laptop, while the **server** is the remote computer that the website is running on (It could be a Linux Distro like Ubuntu).

Pre-requisites:

- 2x Ubuntu 20.04 OS installed and ufw configured,
- Virtual box installed and set up

# **Implement a Client Server Architecture using a Database Management System (MySQL).**

1. Configure 2 new Linux servers in Virtualbox.

Server A - `mysql server` 
Server B - `mysql client`

Step 1 — Installing MySQL (Server)

On mysql server Linux Server, using apt, install the MySQL server software.

```bash
sudo apt update
```

Then I installed the `mysql-server` package:

```bash
sudo apt install mysql-server
```

# **Step 2 — Configuring MySQL**

For fresh installations of MySQL, I am going to run the DBMS’s included security script. This script changes some of the less secure default options for things like remote root logins and sample users.

I run the security script with `sudo`:

```bash
sudo mysql_secure_installation
```

This will take me through a series of prompts where I make some changes to the MySQL installation’s security options. The first prompt will ask whether I would like to set up the Validate Password Plugin, which can be used to test the password strength of new MySQL users before deeming them valid.

For demo purposes I chose less secure option, however If you elect to set up the Validate Password Plugin, any MySQL user you create that authenticates with a password will be required to have a password that satisfies the policy you select. The strongest policy level — which you can select by entering `2` — will require passwords to be at least eight characters long and include a mix of uppercase, lowercase, numeric, and special characters:

```
Output
Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: Y

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG:
 2
```

Regardless of whether I chose to set up the Validate Password Plugin, the next prompt I will set a password for the MySQL root user. Enter and then confirm a secure password of your choice:

```
Output
Please set the password for root here.

New password: 

Re-enter new password:
```

Note that even though I have set a password for the **root** MySQL user, this user is not currently configured to authenticate with a password when connecting to the MySQL shell.

If you used the Validate Password Plugin, you’ll receive feedback on the strength of your new password. Then the script will ask if you want to continue with the password you just entered or if you want to enter a new one. Assuming you’re satisfied with the strength of the password you just entered, enter `Y` to continue the script:

From there, I can press `Y` and then `ENTER` to accept the defaults for all the subsequent questions. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made.

Once the script completes, your MySQL installation will be secured. I can now move on to creating a dedicated database user with the MySQL client.

![Project%205%20-%20Client%20Server%20Architecture%20Using%20A%20MyS%20da7b74da50954f6090f6004eb0a102c5/Untitled%201.png](Project%205%20-%20Client%20Server%20Architecture%20Using%20A%20MyS%20da7b74da50954f6090f6004eb0a102c5/Untitled%201.png)

# **Step 3 — Creating a Dedicated MySQL User and Granting Privileges**

To start with the creation of new mysql user I did the folllowing:

```bash
sudo mysql
```

Command to create the user, the ip is the ip of the client.:

```bash
CREATE USER 'dinul'@'192.168.1.106' IDENTIFIED BY 'Ronaldo7!';
```

Exit SQL

```bash
FLUSH PRIVILEGES;
exit
```

For demo purposes not really creating any new tables leaving the default, the idea of this to show how a client can access the server withoout SSH.

One of the more common problems that users run into when trying to set up a remote MySQL database is that their MySQL instance is only configured to listen for local connections. This is MySQL’s default setting, but it won’t work for a remote database setup since MySQL must be able to listen for an external IP address where the server can be reached. To enable this, I open up mysqld.cnf file:

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Navigated to the line that begins with the bind-address directive. It will look like this:

```bash
													/etc/mysql/mysql.conf.d/mysqld.cnf
. . .
lc-messages-dir = /usr/share/mysql
skip-external-locking
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 127.0.0.1
```

By default, this value is set to 127.0.0.1, meaning that the server will only look for local connections. I have changed this directive to reference an external IP address. For the purposes of troubleshooting, I could set this directive to a wildcard IP address, either *, ::, or 0.0.0.0: but I have decided to use the servers IP address.

I have configured this to the server IP:

```bash
bind-address = 192.168.1.105
```

![Project%205%20-%20Client%20Server%20Architecture%20Using%20A%20MyS%20da7b74da50954f6090f6004eb0a102c5/Untitled%202.png](Project%205%20-%20Client%20Server%20Architecture%20Using%20A%20MyS%20da7b74da50954f6090f6004eb0a102c5/Untitled%202.png)

Restart the mysql service

```bash
sudo systemctl restart mysql
```

UFW allow firewall rules:

Command I used to set to allow client IP on port 3306

```bash
sudo ufw allow from 192.168.1.106 to any port 3306
```

From the client IP I have used the following command to remote on to the server IP.

```bash
mysql -u dinul -h 192.168.1.105 -p
```

I was able to login to my mysql server 192.168.1.105 from client 192.168.1.106

![Project%205%20-%20Client%20Server%20Architecture%20Using%20A%20MyS%20da7b74da50954f6090f6004eb0a102c5/Untitled%203.png](Project%205%20-%20Client%20Server%20Architecture%20Using%20A%20MyS%20da7b74da50954f6090f6004eb0a102c5/Untitled%203.png)

Credits and referece:

[MySQL :: MySQL 8.0 Reference Manual :: 4.2.4 Connecting to the MySQL Server Using Command Options](https://dev.mysql.com/doc/refman/8.0/en/connecting.html)

[How To Install MySQL on Ubuntu 20.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-20-04)

[https://www.digitalocean.com/community/tutorials/how-to-allow-remote-access-to-mysql](https://www.digitalocean.com/community/tutorials/how-to-allow-remote-access-to-mysql)