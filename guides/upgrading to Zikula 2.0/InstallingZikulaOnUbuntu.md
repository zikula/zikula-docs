
#Installation of Zikula on Ubuntu Server

I found installing Zikula on a Ubuntu Server to be quite the process. This had nothing to do with Zikula, but more with getting all the settings for a new Ubuntu server set up correctly so that Zikula would behave. I therefore decided to write a complete tutorial on how to install Zikula on Ubuntu. This tutorial assumes you have a clean install of an Ubuntu Server and was developed with ubuntu 16.04 LTS.

This guide borrows heavily from [the guide at Host Presto](https://hostpresto.com/community/tutorials/how-to-install-zikula-on-ubuntu-16-04/), but there were enough little glitches, I thought I would expand on that and make a more bullet-proof guide. I hope this helps folks with their efforts.

##Requirements
* A new install of [Ubuntu Server](https://www.ubuntu.com/download/server).
* A normal user with sudo priviledges. (If that makes no sense [check this out](https://www.linux.com/learn/linux-101-introduction-sudo%20))

First install your Ubuntu system and once it boots into your desktop, you need to fire up the terminal program. This is located on the left launch bar at the bottom. It looks like a little TV. CLick on it to start. 

##Update the System
You will be using the apt program to download many of the software programs. This is a package manager and is a convenient way to install and update software packages. To update apt type these commands into your terminal program
 
```text
sudo apt-get update -y
sudo apt-get upgrade -y
```
Next restart your system to install these changes

```text
sudo reboot
```

We will now download and add the LAMP stack which stands for Linux Apache, Mysql, and Php. These software programs work together to run your web server and are all needed by Zikula. To download and install them all in one fell swoop, type the following command.

```text
sudo apt-get install apache2 mariadb-server php7.0 php7.0-mysql php7.0-gd php7.0-mcrypt php7.0-xml php7.0-mbstring php7.0-xmlrpc php7.0-curl libapache2-mod-php7.0 wget -y
```
Some explanations about what these do.

* **apache2** is the webserver
* **mariadb-server** is a forked version of mysql. This is the database server that stores content in the database
* **php7.0** installs php 7.0
* **php7.0-mysql php7.0-gd php7.0-mcrypt php7.0-xml php7.0-mbstring php7.0-xmlrpc php7.0-curl libapache2-mod-php7.0** are complimentary php extensions that add functionality that Zikula and its vendor technologies utilize.
* **wget** is a nice program for fetching the latest Zikula release

This may take a while for these packages to download. Once they are all downloaded and installed, then start the apache and mariadb..

```text
sudo systemctl start apache2
sudo systemctl start mysql
sudo systemctl enable apache2
sudo systemctl enable mysql
```
Calling `systemctl start` does what you would expect and starts the service. The `systemctl enable` sets up the service so that it is launch t start up. When you enable the services you will get a notice that the command was redirected to systemd-sysv-install. That is normal

##Set up mariadb using mysql commands
You now want to test to make sure you can launch mysql. Type the following command.
```text
sudo mysql -u root
```
 You have to call this using sudo because the dbserver is protected from root access unless you are the super user. The mysql program should launch and you can create your database and a user for that database using these commands.
 
 ```mysql
CREATE DATABASE zikuladb;
GRANT ALL PRIVILEGES on zikuladb.* TO 'usernamehere'@'localhost' IDENTIFIED BY 'chooseapassword';
FLUSH PRIVILEGES;
```
You can name your database anything you want. The usernamehere and chooseapassword should be a substituted with your own user and password that you want to use. FLUSH PRIVILEGES writes the changes to disk. Quit mysql and make sure you can log in using your newly created user and password by typing this.

```text
mysql -u usernamehere -p
```
Enter your password when prompted then issue the mysql command

```mysql
USE zikuladb;
```
You should see the text, Database changed. This shows you have access to the database. Finally, increase the security on your mysql installation by running this mysql script

```text
mysql_secure_installation
```
Answer the questions as follows.
```text
Set root password? [Y/n] n 
Remove anonymous users? [Y/n] y 
Disallow root login remotely? [Y/n] y 
Remove test database and access to it? [Y/n] y 
Reload privilege tables now? [Y/n] y
```

##Configure apache for Zikula
It is useful to create a virtual host file for Zikula. This allows you to set AllowOverride All which is required for Zikula to activate .htaccess files. If this is not configured correctly, routes don't work right in Zikula. You can do this by creating snipeit.conf file inside /etc/apache2/sites-available/ directory:

```text
sudo nano /etc/apache2/sites-available/zikula.conf
```
Add the following lines to the file.

```xml
<VirtualHost *:80>
    ServerAdmin youremail@domain.com
    DocumentRoot "/var/www/html"
    ServerName yourdomain.com
    <Directory "/var/www/html">
        Options FollowSymLinks
        AllowOverride All
        Order allow,deny
        allow from all
    </Directory>
    ErrorLog /var/log/apache2/zikula-error_log
    CustomLog /var/log/apache2/zikula-access_log common
</VirtualHost>
```
Save and close the file when you are finished, then enable the site with the following command:

```text
sudo a2ensite zikula.conf
```

Next, enable the rewrite module with the following command:
```text
sudo a2enmod rewrite
```

Now test to make sure your configuration is correct and check for any errors in you VirtualHost file

```text
apachectl configtest
```

If you get any errors, fix them, otherwise restart Apache web server so that the changes take place:
```text
sudo systemctl restart apache2
```

##Download and Install Zikula
Download the tar.gz file for the latest release. Right now this is the command to use
```text
wget https://github.com/zikula/core/releases/download/2.0.2/2.0.tar.gz
```
One thing you may run into is the .htaccess file not being read. A nice trick is to put Test in the top of it and it should break your site with a Internal Server Error. This says your .htaccess file is being read. 

The next major issue I ran into was setting RewriteBase correctly in the top .htaccess file. Here is what worked for me.

RewriteBase /zikula/

I hope this addition to the directions at the link help you to install Zikula on Ubuntu