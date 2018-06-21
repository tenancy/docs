---
title: Installation
icon: fal fa-arrow-alt-to-bottom
---


# Elevated database user

Tenancy requires a system connection that allows creating new databases for her
tenants. In order to do so we need to have a database user with elevated
permissions.

A user for both [MariaDB or MySQL][1] and [PostgreSQL][2] would require the "GRANT OPTION" to be
applied. You can either use the root user (for PostgreSQL that is user postgres) or create
your own (recommended):

For MariaDB:
```sql
CREATE DATABASE IF NOT EXISTS tenancy;
CREATE USER IF NOT EXISTS tenancy@localhost IDENTIFIED BY 'someRandomPassword';
GRANT ALL PRIVILEGES ON *.* TO tenancy@localhost WITH GRANT OPTION;
```

For PostgreSQL:
```sql
CREATE DATABASE tenancy;
CREATE USER tenancy WITH CREATEDB CREATEROLE PASSWORD 'someRandomPassword';
GRANT ALL PRIVILEGES ON tenancy to tenancy WITH GRANT OPTION;
```

Make sure you configure this user as your system connection in your `database.php`.
Under connections:

```php
'system' => [
    'driver' => 'mysql',
    'host' => env('TENANCY_HOST', '127.0.0.1'),
    'port' => env('TENANCY_PORT', '3306'),
    'database' => env('TENANCY_DATABASE', 'tenancy'),
    'username' => env('TENANCY_USERNAME', 'tenancy'),
    'password' => env('TENANCY_PASSWORD', ''),
    'unix_socket' => env('DB_SOCKET', ''),
    'charset' => 'utf8mb4',
    'collation' => 'utf8mb4_unicode_ci',
    'prefix' => '',
    'strict' => true,
    'engine' => null,
]
```

Most often it makes sense to use either `system` or `tenant` as your `database.default` setting.
In case you use the `tenant` make sure to enable [early identification](identification) and configure
a decent [fallback](fallback) later on.

> There is no need to configure the `tenant` connection in the `database.php`
configuration file. This connection is set up automatically during runtime. Pre-
configuring this connection may cause unwanted behaviour.

# Package installation

Run the composer command to add the tenancy package as dependency:

```bash
composer require hyn/multi-tenant:5.2.*
```

Laravel offers [package auto discovery][3]
in version 5.6. So that's all you need to do, no need to register any
service providers manually in your `config/app.php`.

Publish the configuration files and migrations for tenancy. This allows you
to configure the behavior of the package.

```bash
php artisan vendor:publish --tag=tenancy
```

Then edit `config/tenancy.php` accordingly.

Adapt the system migrations to your liking inside `database/migrations`, make sure to run them:

```bash
php artisan migrate --database=system
```

> Change `system` to your system connection name.


[1]: https://mariadb.com/kb/en/library/grant/#the-grant-option-privilege
[2]: https://www.postgresql.org/docs/9.6/static/sql-grant.html
[3]: https://medium.com/@taylorotwell/package-auto-discovery-in-laravel-5-5-ea9e3ab20518
