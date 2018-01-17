# 套件依賴
* build-essential
* bison
* flex
* libjpeg-dev
* libpng-dev
* libXpm-dev
* libcurl4-openssl-dev
* libmhash-dev
* libmcrypt-dev
* libreadline-dev
* libedit-dev
* libtidy-dev
* libssl-dev
* systemtap-sdt-dev - dtrace
* libzip-dev
* libxml2-dev
* libicu-dev
* libxslt-dev
* libpcre3-dev
* automake
* autoconf
* libmysqlclient-dev - php5.2
* libsodium-dev - php7.2
* argon2 https://github.com/P-H-C/phc-winner-argon2

```
# apt install build-essential bison flex libjpeg-dev libpng-dev libXpm-dev libcurl4-openssl-dev libmhash-dev libmcrypt-dev libmysqlclient-dev libreadline-dev libedit-dev libtidy-dev libssl-dev systemtap-sdt-dev libzip-dev libxml2-dev libicu-dev libxslt-dev libpcre3-dev libsodium-dev automake autoconf
```

# 編譯 argon2

```
# apt install git
# cd /usr/src
# git clone https://github.com/P-H-C/phc-winner-argon2
# cd phc-winner-argon2
# git checkout 20171227
# make install
```

# 安裝 apache2

```
# apt install apache2
```

# 下載 Source Code

* php 7.2.1 - http://php.net/downloads.php
* php 5.6.33 - http://php.net/downloads.php
* php 5.2.17 - http://php.net/releases/

# 下載 PHP 5.2 Patch

* PHP-FPM - https://php-fpm.org/downloads/
* libxml2 - https://mail.gnome.org/archives/xml/2012-August/txtbgxGXAvz4N.txt
* openssl-1.0.0 sslv2 - https://github.com/centos-bz/ezhttp/blob/master/conf/debian_patches_disable_SSLv2_for_openssl_1_0_0.patch
* fastcgi apache patch - patch/php-5.2.17-fcgi-script-filename.diff

# 打 PHP 5.2 補丁

patch
```
# cd php-5.2.17
# zcat ../php-5.2.17-fpm-0.5.14.diff.gz|patch -p1
# patch -p0 < ../txtbgxGXAvz4N.txt
# patch -p1 < ../debian_patches_disable_SSLv2_for_openssl_1_0_0.patch
# patch -p1 < ../php-5.2.17-fcgi-script-filename.diff
```

resolve libs path
```
#ln -sf /usr/lib/x86_64-linux-gnu/libjpeg.so /usr/lib/
#ln -sf /usr/lib/x86_64-linux-gnu/libjpeg.a /usr/lib/
#ln -sf /usr/lib/x86_64-linux-gnu/libpng.so /usr/lib/
#ln -sf /usr/lib/x86_64-linux-gnu/libpng.a /usr/lib/
#ln -sf /usr/lib/x86_64-linux-gnu/libXpm.so /usr/lib/
#ln -sf /usr/lib/x86_64-linux-gnu/libXpm.a /usr/lib/
#ln -sf /usr/lib/x86_64-linux-gnu/libmysqlclient.so /usr/lib/
#ln -sf /usr/lib/x86_64-linux-gnu/libmysqlclient.a /usr/lib/
```

# 編譯 PHP

```
# cd php-xxx
# ./setup-xxx
# make clean
# make install
```

# 設定 FPM

php 7.2 
```
# cd /opt/php/7.2/etc
# mv php-fpm.conf.default php-fpm.conf
# cd /opt/php/7.2/etc/php-fpm.d
# mv www.conf.default www.conf
# cp [source_path]/php.ini-production /opt/php/7.2/etc/php.ini
```

php 5.6
```
# cd /opt/php/5.6/etc
# mv php-fpm.conf.default php-fpm.conf
# cp [source_path]/php.ini-production /opt/php/5.6/etc/php.ini
```

php 5.2
```
# cd /opt/php/5.2/etc
# mv php-fpm.conf.default php-fpm.conf
# cp [source_path]/php.ini-production /opt/php/5.2/etc/php.ini
```

# 設定 Apache

```
# a2enmod proxy
# a2enmod proxy_fcgi
```

/etc/apache2/sites-enabled/000-default.conf
```
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html


        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
<IfModule proxy_fcgi_module>
ProxyPassMatch "^/php7/(.*\.php(/.*)?)$" "fcgi://localhost:9000/var/www/html"
ProxyPassMatch "^/php56/(.*\.php(/.*)?)$" "fcgi://localhost:9001/var/www/html"
ProxyPassMatch "^/php52/(.*\.php(/.*)?)$" "fcgi://localhost:9002/var/www/html"
</IfModule>
</VirtualHost>
```

