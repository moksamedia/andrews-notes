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
### Manage Users

Can either make apache2 run with my local user by editing `/etc/apache2/apache2.conf` or can keep apache running with `www-data` and change 
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
