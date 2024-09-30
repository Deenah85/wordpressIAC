Go to https://app.vagrantup.com/ubuntu/boxes/focal64 to find the image name
open Git bash
use command vagrant init "ubuntu/focal6"

Open IDE to edit vagrant file
modify network as preferred (check vagrant file in this repo)

The steps are:

**Install Dependencies
**
To install PHP and Apache, use following command:
sudo apt update
sudo apt install apache2 \
                 ghostscript \
                 libapache2-mod-php \
                 mysql-server \
                 php \
                 php-bcmath \
                 php-curl \
                 php-imagick \
                 php-intl \
                 php-json \
                 php-mbstring \
                 php-mysql \
                 php-xml \
                 php-zip -y

**Install WordPress
**
sudo mkdir -p /srv/www
sudo chown www-data: /srv/www
curl https://wordpress.org/latest.tar.gz | sudo -u www-data tar zx -C /srv/www

**Configure Apache for WordPress
**
Create Apache site for WordPress. Create /etc/apache2/sites-available/wordpress.conf with following lines, and define the root folder:

<VirtualHost *:80>
    DocumentRoot /srv/www/wordpress
    <Directory /srv/www/wordpress>
        Options FollowSymLinks
        AllowOverride Limit Options FileInfo
        DirectoryIndex index.php
        Require all granted
    </Directory>
    <Directory /srv/www/wordpress/wp-content>
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>

**Enable the site with:
**
sudo a2ensite wordpress

**Enable URL rewriting with:
**
sudo a2enmod rewrite

**Disable the default “It Works” site with:
**
sudo a2dissite 000-default

**reload apache2 to apply all these changes:
**
sudo service apache2 reload

**Configure database
**
sudo mysql -u root
mysql> CREATE DATABASE wordpress;
mysql> CREATE USER wordpress@localhost IDENTIFIED BY 'P@ssw0rd';
mysql> GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON wordpress.* TO wordpress@localhost;
mysql> FLUSH PRIVILEGES;
mysql> quit

**Configure WordPress to connect to the database
**
sudo -u www-data cp /srv/www/wordpress/wp-config-sample.php /srv/www/wordpress/wp-config.php

sudo -u www-data sed -i 's/database_name_here/wordpress/' /srv/www/wordpress/wp-config.php
sudo -u www-data sed -i 's/username_here/wordpress/' /srv/www/wordpress/wp-config.php
sudo -u www-data sed -i 's/password_here/P@ssw0rd/' /srv/www/wordpress/wp-config.php

**in a terminal session open the configuration file in nano:
**
sudo -u www-data nano /srv/www/wordpress/wp-config.php
Find the following:
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );

Delete those lines (ctrl+k will delete a line each time you press the sequence). Then replace with the content of https://api.wordpress.org/secret-key/1.1/salt/.

(This address is a randomiser that returns completely random keys each time it is opened.) This step is important to ensure that your site is not vulnerable to “known secrets” attacks.

Login to ip address
