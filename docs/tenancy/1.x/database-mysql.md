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

There are several ways of setting the tenant database configuration
for MySQL:

- Re-using a connection configured in `database.php` under `connections`.
- Listen to the `Tenancy\Hooks\Database\Events\Drivers\Configuring` event to further change the configuration.
