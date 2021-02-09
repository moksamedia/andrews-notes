### Links
- https://www.linuxhelp.com/how-to-install-lamp-on-popos
- https://askubuntu.com/questions/537032/how-to-configure-apache2-with-symbolic-links-in-var-www
- https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-phpmyadmin-on-ubuntu-20-04

### Update Ports
```conf
Listen 8000

<IfModule ssl_module>
    Listen 8443
</IfModule>

<IfModule mod_gnutls.c>
    Listen 8443
</IfModule>
```
### Enable mod_rewrite and allow overrides

Symptom of this is that homepage and admin load but other pages give a 404

```bash
sudo a2enmod rewrite
```

`/etc/apache2/apache2.conf`
```bash
<Directory /var/www/>
	Options Indexes FollowSymLinks
	AllowOverride All  # <-- UPDATE ME
	Require all granted
</Directory>
```

### Manage Users

Can either make apache2 run with my local user by editing `/etc/apache2/envvars` or can keep apache running with `www-data` and change 
permissions of web files and folders. Don't have to change ports if you let apache run as `www-data`.

```bash
chown www-data:www-data  -R * # Let Apache be owner
find . -type d -exec chmod 755 {} \;  # Change directory permissions rwxr-xr-x
find . -type f -exec chmod 644 {} \;  # Change file permissions rw-r--r--
```
If keeping apache2 on www-data, add my user to www-data group:
```bash
sudo adduser {username} www-data
```

Execute command as `www-data`
```bash
sudo -u www-data <the command>
```

### Virtual Hosts

Create a virtual host site in `/etc/apache2/sites-available` called learntibetanlanguage.conf

```conf
<VirtualHost learntibetanlanguage.local:8000>

  ServerName learntibetanlanguage.local
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/learntibetanlanguage

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

```bash
a2ensite learntibetanlanguage
```

Edit `/etc/hosts`

```bash
127.0.0.1 learntibetanlanguage.local
```

Create a symlink from `/var/www/html` to development location.
```bash
ln -s ~/Development/learntibetanlanguage /var/www/learntibetanlanguage
chmod o+x var/www/learntibetanlanguage
```

### /etc/apache2/sites-available/skbm.conf
```conf
<VirtualHost local.skbm:8443>

    SSLEngine on
    ServerName local.skbm
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html/skbm

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

		SSLCertificateFile      /home/andrewcarterhughes/Development/skbm/local.skbm.crt
		SSLCertificateKeyFile   /home/andrewcarterhughes/Development/skbm/local.skbm.key

</VirtualHost>

<VirtualHost local.skbm:8000>

    ServerName local.skbm
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html/skbm

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

### /etc/apache2/sites-available/phpmyadmin.conf
```conf
<VirtualHost phpmyadmin:8000>

        ServerName localhost

        <Directory /usr/share/phpmyadmin>
                AllowOverride None
                Require all granted
        </Directory>

        DocumentRoot /usr/share/phpmyadmin

        Include /etc/phpmyadmin/apache.conf

        ErrorLog ${APACHE_LOG_DIR}/phpmyadmin.error.log
        CustomLog ${APACHE_LOG_DIR}/phpmyadmin.access.log combined

</VirtualHost>
```

### MariaDB Commands
```bash
SELECT User,Host,Plugin FROM mysql.user;
update mysql.user set plugin = 'mysql_native_password' where User='root';
update user SET PASSWORD=PASSWORD("root") WHERE USER='root';
FLUSH PRIVILEGES;
```

https://www.digitalocean.com/community/tutorials/how-to-reset-your-mysql-or-mariadb-root-password

