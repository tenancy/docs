---
title: Filesystem
icon: fal fa-hdd
---

You are now, most likely, aware about the [directory structure][directory-structure]
available to tenants. 

# Tenant disk

To access the directory of the active tenant, you can use the `tenant` disk. 

```php
Storage::disk('tenant')->put('readme.md', 'Hi John.');
```

> Tenants files are by default stored to `storage/app/tenancy`, but this can be reconfigured
in the `tenancy.php` under `website > disk`. 

Read more about disks on the [Laravel documentation][laravel-filesystem].

# Directory

In order to assist with mutating these contents, you can use the
Directory class available in this package.

```php
$directory = app(Hyn\Tenancy\Website\Directory::class);
```

This class is set to use the tenant currently active, but also allows you to activate
any other tenant whenever you need to using the `setWebsite(Website $website)` method.

The Directory class implements `Illuminate\Contracts\Filesystem\Filesystem` which offers
the same methods you are used to, including; `get`, `put` and `setVisibility`.

# Tenancy directory

In case you prefer having a higher level access to the files for tenants. You can use the ioc bound
Filesystem.

```php
/** @var \Illuminate\Contracts\Filesystem\Filesystem $tenancy */
$tenancy = app('tenancy.disk');
```

[directory-structure]: structure
[laravel-filesystem]: https://laravel.com/docs/5.6/filesystem
