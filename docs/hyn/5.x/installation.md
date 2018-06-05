---
title: Installation
icon: fal fa-arrow-alt-to-bottom
---
Run the composer command to add the tenancy package as dependency:

```bash
composer require hyn/multi-tenant:5.*
```

Laravel offers [package auto discovery](https://medium.com/@taylorotwell/package-auto-discovery-in-laravel-5-5-ea9e3ab20518)
in version 5.5. So that's all you need to do, no need to register any
service providers manually in your `config/app.php`.

Optionally you can publish the configuration files for tenancy. This allows you to configure
the system database before migrating tables into your tenancy system database, run:

```bash
php artisan vendor:publish --tag=tenancy
```

Then edit `config/tenancy.php` accordingly.

Now enable the tenancy environment by running the installation procedure;

```bash
php artisan tenancy:install
```

> The `tenancy:install` command migrates the system tables.
