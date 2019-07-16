---
title: Database driver MySQL
icon: fal fa-warehouse-alt
excerpt: >
    Maintains the tenant MySQL database.
tags:
    - database
    - tenant
    - driver
    - mysql
---
Install using composer:

```bash
$ composer require tenancy/db-driver-mysql
```

## Configuration

Publish the `db-driver-mysql.php` config file using artisan.

```bash
$ php artisan vendor:publish --tag db-driver-mysql
```
There are several ways of setting the tenant database configuration
for MySQL:

- Re-using a connection configured in `database.php` under `connections`.
- Providing a `preset` which the driver uses to generate a unique configuration from.
- Listen to the `Tenancy\Database\Events\Drivers\Configuring` event to further change the configuration.
