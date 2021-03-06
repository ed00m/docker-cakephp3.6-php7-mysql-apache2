# CakePHP 3.6 Docker Container

A [CakePHP Docker Container][docker] which installs a fresh, fully configured
and operational version of CakePHP 3.6. It comes with two pre-configured
databases, for both production and testing environments and the app runs on PHP
7.0 up to 7.3, Apache 2.4 (or Nginx 1.14) and MariaDB 10.1 (read the _MariaDB
versus MySQL_ note below on why MariaDB was chosen over MySQL).

<!-- TOC START min:2 max:6 link:true update:true -->
- [Installation](#installation)
- [Supported Tags and respective `Dockerfile` links](#supported-tags-and-respective-dockerfile-links)
    - [Apache PHP 7](#apache-php-7)
        - [mod_php](#mod_php)
        - [php-fpm](#php-fpm)
    - [Nginx PHP-FPM 7](#nginx-php-fpm-7)
- [Modifying CakePHP Settings](#modifying-cakephp-settings)
- [MariaDB versus MySQL](#mariadb-versus-mysql)
- [Accessing the Databases](#accessing-the-databases)
    - [Production Database](#production-database)
    - [Testing Database](#testing-database)
    - [Changing MySQL Passwords](#changing-mysql-passwords)
        - [Setting the MySQL Root Password](#setting-the-mysql-root-password)
- [What is CakePHP?](#what-is-cakephp)
- [Quick reference](#quick-reference)

<!-- TOC END -->
![CakePHP 3.6.11][screenshot]

## Installation

Installation is dead-easy. Just run the following command and then visit
your browser (e.g. http://192.168.99.1) to see your newly configured CakePHP
website.

```docker
docker run -p 80:80 justinhartman/cakephp3.5-php7-mysql-apache2:latest
```

## Supported Tags and respective `Dockerfile` links

Below are tags to various PHP versions running either `Apache` or `Nginx` which
come pre-installed in all the containers. Pick the version of PHP and web-server
of your choosing, based on the tags below, when installing or running a
container.

### Apache PHP 7

You have two options when installing PHP with Apache 2.4. These are the built in
Apache 2 PHP module (`mod_php`) or the stand-alone FastCGI PHP server
(`php-fpm`). You can install either `mod_php` or `php-fpm` by selecting one of
the tags below.

#### mod_php

- `apache-php-7.3.0`, `apache-php-7.3`, `apache-7.3` [(apache/php/7.3/Dockerfile)][apache-7.3]
- `apache-php-7.2.9`, `apache-php-7.2`, `apache-7.2`, `latest` [(apache/php/7.2/Dockerfile)][apache-7.2]
- `apache-php-7.1.20`, `apache-php-7.1`, `apache-7.1` [(apache/php/7.1/Dockerfile)][apache-7.1]
- `apache-php-7.0.31`, `apache-php-7.0`, `apache-7.0` [(apache/php/7.0/Dockerfile)][apache-7.0]

#### php-fpm

- `apache-fpm-7.3.0`, `apache-fpm-7.3` [(apache/php-fpm/7.3/Dockerfile)][apache-fpm-7.3]
- `apache-fpm-7.2.9`, `apache-fpm-7.2` [(apache/php-fpm/7.2/Dockerfile)][apache-fpm-7.2]
- `apache-fpm-7.1.20`, `apache-fpm-7.1` [(apache/php-fpm/7.1/Dockerfile)][apache-fpm-7.1]
- `apache-fpm-7.0.31`, `apache-fpm-7.0` [(apache/php-fpm/7.0/Dockerfile)][apache-fpm-7.0]

The `mod_php` and `php-fpm` Docker containers also come with the following
additional software installed:

1. CakePHP 3.6.11
1. Apache 2.4.34
1. MariaDB 10.1.35

### Nginx PHP-FPM 7

By default the Nginx Docker containers all run on the stand-alone FastCGI PHP
server (`php-fpm`) and are pre-configured with this deployment of PHP.

- `nginx-fpm-7.3.0`, `nginx-fpm-7.3`, `nginx-7.3` [(php/nginx/7.3/Dockerfile)][nginx-7.3]
- `nginx-fpm-7.2.9`, `nginx-fpm-7.2`, `nginx-7.2` [(php/nginx/7.2/Dockerfile)][nginx-7.2]
- `nginx-fpm-7.1.20`, `nginx-fpm-7.1`, `nginx-7.1` [(php/nginx/7.1/Dockerfile)][nginx-7.1]
- `nginx-fpm-7.0.31`, `nginx-fpm-7.0`, `nginx-7.0` [(php/nginx/7.0/Dockerfile)][nginx-7.0]

The `nginx` containers also come with the following additional software
installed:

1. CakePHP 3.6.11
1. Nginx 1.14.0
1. MariaDB 10.1.35

## Modifying CakePHP Settings

There is [dotenv support][dotenv] which has been made available in your
`/config/bootstrap.php` file. You can access and modify any settings you'd like
to make by editing `/config/.env`. Do not make changes to either
`/config/app.php` or `/config/app.default.php` as it is better to work off the
`.env` file for any configuration changes.

The `.env` file has configuration options for:

- General app settings
- Cache control
- Email transport
- Database connection
- Logging options
- Session and Error level handling

## MariaDB versus MySQL

MariaDB 10.1 is installed due to a bug with MySQL 5.7 that prevents it
installing when building on Docker Hub. Ironically, if you build the image on
your machine MySQL 5.7 builds fine so the issue, for whatever reason, only
exists on Docker Hub. If you need MySQL 5.7 you could change the
`mariadb-server` option to `mysql-server` by editing the `Dockerfile` and
building the image yourself.

## Accessing the Databases

The containers come with MariaDB pre-installed. During the Docker creation, two
databases are installed for both production and any unit tests you need to run.

### Production Database

Database: `cakephp_live`  
Username: `cakephp_live_user`  
Password: `b5uF95fstJmhABD4is`  
Connection: `DATABASE_URL` exists in your `/config/.env` file with the full
connection string to the production database.

### Testing Database

Database: `cakephp_test`  
Username: `cakephp_test_user`  
Password: `4hiEKuzgFr54fyPVQJ`  
Connection: `DATABASE_TEST_URL` exists in your `/config/.env` file with the full
connection string to the testing database.

### Changing MySQL Passwords

If you'd like to change the password for the two default MySQL user accounts
then run the following commands in MySQL - replace `$PASSWORD` with your new
password for the account.

```mysql
mysql> FLUSH PRIVILEGES;
mysql> ALTER USER 'cakephp_live_user'@'localhost' IDENTIFIED BY '$PASSWORD';
mysql> ALTER USER 'cakephp_test_user'@'localhost' IDENTIFIED BY '$PASSWORD';
```

#### Setting the MySQL Root Password

The MySQL root password has not been set in the Docker container.
Please make sure to change this and set a password for your `root` MySQL
account as it is not secure if left as is. You can follow the same method above
to set a `root` password or you could run `$ mysql_secure_installation` in a
terminal window in the Container Image which would completely secure your
MySQL installation.

## What is CakePHP?

[CakePHP][cakephp] is an open source web application framework. It follows the
Model-View-Controller (MVC) approach and is written in PHP, modelled after the
concepts of Ruby on Rails, and distributed under the MIT License.

## Quick reference

- **Where to file issues**:  
  [@justinhartman/docker-cakephp3.6-php7-mysql-apache2/issues][issues]
- **Maintained by**:  
  [Justin Hartman][justinhartman]
- **Supported Docker versions**:  
  [the latest release][releases] (down to 1.6 on a best-effort basis)

[screenshot]: https://raw.githubusercontent.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/master/cakephp_3.6.11.png
[cakephp]: http://cakephp.org
[docker]: https://hub.docker.com/r/justinhartman/cakephp3.5-php7-mysql-apache2/
[dotenv]: https://github.com/josegonzalez/php-dotenv
[justinhartman]: https://github.com/justinhartman
[issues]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/issues
[releases]: https://github.com/docker/docker-ce/releases/latest
[apache-7.3]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/blob/739aa5c2c4568c93e4fcfb3c66e3671ecb19b87e/apache/php/7.3/Dockerfile
[apache-7.2]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/blob/739aa5c2c4568c93e4fcfb3c66e3671ecb19b87e/apache/php/7.2/Dockerfile
[apache-7.1]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/blob/739aa5c2c4568c93e4fcfb3c66e3671ecb19b87e/apache/php/7.1/Dockerfile
[apache-7.0]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/blob/739aa5c2c4568c93e4fcfb3c66e3671ecb19b87e/apache/php/7.0/Dockerfile
[apache-fpm-7.3]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/blob/8d1f255c20cfbc8777374e4dc3e61ba67a832b5e/apache/php-fpm/7.3/Dockerfile
[apache-fpm-7.2]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/blob/739aa5c2c4568c93e4fcfb3c66e3671ecb19b87e/apache/php-fpm/7.2/Dockerfile
[apache-fpm-7.1]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/blob/8d1f255c20cfbc8777374e4dc3e61ba67a832b5e/apache/php-fpm/7.1/Dockerfile
[apache-fpm-7.0]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/blob/8d1f255c20cfbc8777374e4dc3e61ba67a832b5e/apache/php-fpm/7.0/Dockerfile
[nginx-7.3]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/blob/7b3aa5f08e1d228a7e30e81d4826759ddc3e948a/nginx/php-fpm/7.3/Dockerfile
[nginx-7.2]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/blob/7b3aa5f08e1d228a7e30e81d4826759ddc3e948a/nginx/php-fpm/7.2/Dockerfile
[nginx-7.1]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/blob/7b3aa5f08e1d228a7e30e81d4826759ddc3e948a/nginx/php-fpm/7.1/Dockerfile
[nginx-7.0]: https://github.com/justinhartman/docker-cakephp3.6-php7-mysql-apache2/blob/7b3aa5f08e1d228a7e30e81d4826759ddc3e948a/nginx/php-fpm/7.0/Dockerfile
