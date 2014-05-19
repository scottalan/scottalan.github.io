---
layout: post
title: "OSX 10.8: Adding PHP Versions for IDE Development"
date: 2014-04-29 12:30:01 -0500
comments: true
categories: 
---

You will need [Xcode and Command Line Tools](https://developer.apple.com/downloads/index.action) installed.

You don't have to use [Fink](http://www.finkproject.org/) as your package manager but I find it nice to be able to just use the commands I am used to
when working on a server.

Download the version of Fink you need for your OS http://www.finkproject.org/download/index.php?phpLang=en

Allow Fink to install to its default location `/sw`

I will assume you can handle the above so we will jump right in...

`mkdir -p /opt/src`

Found [this snippet](https://github.com/wilmoore/php-version/wiki/Building-PHP-Versions#do-it-yourself-recommended-for-those-that-need-more-control)
that I started with to handle the first part of getting everything into place, but had to alter it a bit so that it would work for my use-case.

```
#!/bin/bash

if [ $# -ne 1 ]; then
  echo "Usage: $0 expects at least one argument: [php version]"
  echo "i.e., 5.3.28 - http://php.net/downloads.php"
  exit
fi

PHP_TARGET_VERSION=${1}

PHP_INSTALL_PREFIX=/opt/phps

mkdir -p /tmp/src/php-${PHP_TARGET_VERSION}

cd /tmp/src/php-${PHP_TARGET_VERSION}

curl -# -L http://us1.php.net/get/php-${PHP_TARGET_VERSION}.tar.gz/from/this/mirror | tar -xz --strip 1

echo "Target Version: ${PHP_TARGET_VERSION}"
echo "Install Prefix: ${PHP_INSTALL_PREFIX}"

./configure \
            --sysconfdir=/private/etc \
            --prefix=${PHP_INSTALL_PREFIX}/${PHP_TARGET_VERSION} \
            --with-config-file-path=${PHP_INSTALL_PREFIX}/${PHP_TARGET_VERSION}/etc \
            --mandir=${PHP_INSTALL_PREFIX}/${PHP_TARGET_VERSION}/share/man \
            --infodir=${PHP_INSTALL_PREFIX}/${PHP_TARGET_VERSION}/share/info \
            --enable-debug

make

sudo make install

# Additional flags that can be set.

#            --with-apxs2=/usr/sbin/apxs \
#            --enable-cli \
#            --enable-debug \
#            --with-libxml-dir=/usr \
#            --with-openssl=/usr \
#            --with-kerberos=/usr \
#            --with-zlib=/usr \
#            --enable-bcmath \
#            --with-bz2=/usr \
#            --enable-calendar \
#            --disable-cgi \
#            --with-curl=/usr \
#            --enable-dba \
#            --enable-exif \
#            --enable-ftp \
#            --with-gd \
#            --with-freetype-dir=/sw \
#            --with-jpeg-dir=/sw \
#            --with-png-dir=/sw \
#            --enable-gd-native-ttf \
#            --with-icu-dir=/usr \
#            --with-iodbc=/usr \
#            --with-ldap=/usr \
#            --with-ldap-sasl=/usr \
#            --with-libedit=/usr \
#            --enable-mbstring \
#            --enable-mbregex \
#            --with-mysql=mysqlnd \
#            --with-mysqli=mysqlnd \
#            --without-pear \
#            --with-pdo-mysql=mysqlnd \
#            --with-mysql-sock=/var/mysql/mysql.sock \
#            --with-readline=/usr \
#            --enable-shmop \
#            --with-snmp=/usr \
#            --enable-soap \
#            --enable-sockets \
#            --enable-sysvmsg \
#            --enable-sysvsem \
#            --enable-sysvshm \
#            --with-tidy \
#            --enable-wddx \
#            --with-xmlrpc \
#            --with-iconv-dir=/usr \
#            --with-xsl=/usr \
#            --enable-zip \
#            --with-pcre-regex=/usr \
#            --with-pgsql=/usr \
#            --with-pdo-pgsql=/usr \
```


You can then run: `sudo php-configure.sh 5.5.11` This assumes you have added the script to your $PATH.

Download this: https://github.com/wilmoore/php-version

Then you can export your path:

`export PHP_VERSIONS="/opt/phps"`

Type: `which php` and you should see you are using the default: `/usr/bin`

This gets you, at a basic level, the versions of php you may need for your IDE.

In your IDE you can set the interpreter to: `/opt/phps/5.5.11/bin` for example...
