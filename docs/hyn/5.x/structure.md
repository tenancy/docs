---
title: Structure
icon: fal fa-sitemap
---

# Introduction

The location of the tenant specific files is configured inside the `config/tenancy.php`
file under `website > disk`. By default tenant files 
are stored in `storage/app/tenancy/tenants`.

Inside this tenant directory you are able to override global logic by
generating the necessary files. Check the tenancy configuration file under
`folders` to see which ones are enabled by default.

## Config

Any php files stored inside a `config/` directory of the tenant is read
during runtime. Existing configuration values are replaced and new ones
are appended.

A great use case for this feature is the ability to configure client 
specific mail settings.

The tenancy configuration file allows you to refuse overrides of specific
configuration settings using the `folders > config > blacklist` setting.
By default it disallows database, tenancy and webserver changes.

## Routes

Create a `routes.php` file inside your tenant directory to configure
new routes during runtime. Using a configurable prefix, any unique
url's will be matched for this specific tenant only.

## Trans

New translations can be easily added by creating a `lang/` folder.
The native Laravel translator will parse any files contained within
to be used in the application of this specific tenant.

You are able to specify whether these language files should override
the global, system-wide translation. In case you decide against that
feel free to set up a translation namespace.

## Vendor

A very powerful addition is the ability to inject new packages and code.
Whenever the tenant directory has a `vendor/autoload.php` file it will
be loaded during runtime. Using that specific file allows you to fully
utilize the power of composer inside your tenant directories.

> Please be aware that no compatibility checks are executed against your app.
Identifying whether a composer conflict could happen is something you should test
outside production environments.

## Media

The media folder can be used by creating a `media/` folder inside the
tenant directory. Files inside that directory will become automatically
available as a public directory inside an equally named directory `media/`.

Eg, storing a file `media/bg.jpg` will allow you to use `/media/bg.jpg` inside
your html. This applies to any file you store.

## /media webserver

An alias is set up binding the private tenant media directory to the public
media directory. This is implemented using the auto generated web server
configuration files for apache and nginx.

## /media fallback controller

In case you're not using the webserver vhost configuration files provided
by this package you can choose to map the `MediaController` to the path, eg:

```php
Route::get('/media/{path}', Hyn\Tenancy\Controllers\MediaController::class)
    ->where('path', '.+')
    ->name('tenant.media');
```
