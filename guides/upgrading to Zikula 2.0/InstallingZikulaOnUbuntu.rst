============
Installation of Zikula on Ubuntu Server
============

I found installing Zikula on a Ubuntu Server to be quite the process. This had nothing to do with Zikula, but more with getting all the settings for a new Ubuntu server set up correctly so that Zikula would behave. I therefore decided to write a complete tutorial on how to install Zikula on Ubuntu. This tutorial assumes you have a clean install of an Ubuntu Server and was developed with ubuntu 16.04 LTS.

First follow the directions on this link exactly.

https://hostpresto.com/community/tutorials/how-to-install-zikula-on-ubuntu-16-04/

I had one sticking point in that the mariadb got a little fussy when I was trying to create db users and the database. The solution was to log in as root with the following command

sudo mysql -u root

That would allow you access to the db. If you didn't use sudo, you would get an access denied error. From there you can go on and create a new user and a database and give access to that db to the user. You will use these to set up Zikula.

You then later in the directions need to configure the apache webserver. Here is the settings I used to get it to work on a local test server. You would of course change the server name to get it to work on a production site. Here are the settings I used.

..  code-block:: html
    :linenos:

  <VirualHost *:80>  
  ServerAdmin youremail@domain.com
  DocumentRoot "/var/www/html"
  ServerName localhost
  <Directory "/var/www/html">
  Options FollowSymLinks
  AllowOverride All
  Order allow,deny
  allow from all
  </Directory>
  ErrorLog /var/log/apache2/zikula-error_log
  CustomLog /var/log/apache2/zikula-access_log common
  </VirtualHost>
::

One thing you may run into is the .htaccess file not being read. A nice trick is to put Test in the top of it and it should break your site with a Internal Server Error. This says your .htaccess file is being read. 

The next major issue I ran into was setting RewriteBase correctly in the top .htaccess file. I was installing Zikula into a folder inside the root directory. The name of the folder was zikula. Here is what worked for me.

RewriteBase /zikula/

I hope this addition to the directions at the link help you to install Zikula on Ubuntu
