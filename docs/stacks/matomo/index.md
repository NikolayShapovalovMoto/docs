# Matomo stack documentation

## Setup

Use the following credentials during the inital setup on the Database Setup step:

* Database Server: `matomo`
* Login: `matomo`
* Password: Copy the value of `MYSQL_PASSWORD` from `[Instance] > Stack > Database` page
* Database Name: `matomo`

## Redis integration

You can use redis to store Matomo cachet (by default stored in the local filesystem):

* Enable redis service in your application stack from `[Instance] > Stack` page
* Copy your instance UUID from `[Instance] > Settings` page
* Access the server hosting your matomo instance as root and append `/srv/wodby/instances/[INSTANCE UUID]/app/config/config.ini.php` file with the following values:

```
[Cache]
backend = chained

[ChainedCache]
backends[] = array
backends[] = redis

[RedisCache]
host = "redis"
port = 6379
timeout = 0.0
password = "Copy the value of `REDIS_PASSWORD` from `[Instance] > Stack > Redis page`"
database = 14
```

## Email configuration

Go to `Settings > System > General settings > Email server settings` in your Matomo instance. Specify `opensmtpd` as server address and `25` as port. For more details how to configure guaranteed email delivery see [OpenSMTPD stack documentation](../opensmtpd/index.md)

## Changelog

### 0.3.1

* Matomo updated to 3.5.1
* Added Nginx 1.14, 1.15
* MariaBD:
  * Added new version 10.3
  * Version 10.2 updated to 10.2.15
  * Default `innodb_buffer_pool_size` set to `128M` that should significantly decrease memory usage
* PHP error reporting now exludes strict and deprecated errors

### 0.3.0

* Matomo updated to 3.5.0
* Added SSHD container
* Matomo image rebased to latest stable PHP 7.1 image (`wodby/php:7.1-4.4.2`)

### 0.2.1

* Bugfix: cron task failed

### 0.2.0

* PHP updated to 7.1.15 (security updates)
* Redis service added
* Docs: added instructions for redis and email configuration
* Bugfix: insufficient permissions for plugins install

### 0.1.0

* Initial release