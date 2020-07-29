---
title: Installation
icon: fal fa-arrow-alt-to-bottom
---
# Installation

1. [Simplified Install](#simplified-install)
2. [Selective Install](#selective-install) (*Recommended*)

## Simplified Install

For a quick, simplified installation you can install everything at once:

```bash
composer require tenancy/tenancy
```

> CAUTION: This will load specific non-intrusive Affects by default. You can check which are loaded by looking in the `composer.json` of the repository.

This is an easy way of giving this toolkit a spin!

> We recommend selectively installing the packages you need.

After you've installed tenancy/tenancy make sure you register the Affects and Hooks you wish to use. You do this by simply registering the respective Providers.
Here is the non-exhaustive list of providers that can be added to `config/app.php`, make sure you comment out the ones you don't need.
The exact provider needed is included in each component's documentation.

```php
    'providers' => [
         // ...

        /*
         * Package Service Providers...
         */
        Tenancy\Affects\Broadcasts\Provider::class,
        Tenancy\Affects\Cache\Provider::class,
        Tenancy\Affects\Configs\Provider::class,
        Tenancy\Affects\Connections\Provider::class,
        Tenancy\Affects\Filesystems\Provider::class,
        Tenancy\Affects\Logs\Provider::class,
        Tenancy\Affects\Mails\Provider::class,
        Tenancy\Affects\Models\Provider::class,
        Tenancy\Affects\Routes\Provider::class,
        Tenancy\Affects\URLs\Provider::class,
        Tenancy\Affects\Views\Provider::class,

        Tenancy\Hooks\Database\Provider::class,
        Tenancy\Hooks\Migration\Provider::class,
        Tenancy\Hooks\Hostname\Provider::class,

        Tenancy\Database\Drivers\Mysql\Provider::class,
        Tenancy\Database\Drivers\Sqlite\Provider::class,
         // ...
    ]
```

## Selective Install

Instead of loading all tenancy packages at once, you can selectively install
what you need. You need at least the framework:

```bash
composer require tenancy/framework
```

After that selectively add:

- [Database drivers](database-drivers). Where to allocate tenant information.
- [Identification drivers](identification-general). Allowing you to configure how your tenant is identified.
- [Affects](affects-general). How your application is modified once a tenant is identified.
- [Lifecycle hooks](hooks-general). Impact your application whenever tenants are created, updated or deleted.
