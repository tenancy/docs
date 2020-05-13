---
title: Installation
icon: fal fa-arrow-alt-to-bottom
---
For a quick, simplified installation you can install everything at once:

```bash
composer require tenancy/tenancy
```

This is an easy way of giving this toolkit a spin!

> We recommend selectively installing the packages you need.

After you've installed tenancy/tenancy make sure to register the ServiceProvider
for the [database driver](database-drivers) you wish to use in your app.

## Selective install

Instead of loading all tenancy packages at once, you can selectively install
what you need. You need at least the framework:

```bash
composer require tenancy/framework
``` 

After that selectively add:

- [Database drivers](database-drivers). Where to allocate tenant information.
- [Identification drivers](identification-drivers). Allowing you to configure how your tenant is identified.
- [Affects](affects). How your application is modified once a tenant is identified.
- [Lifecycle hooks](hooks). Impact your application whenever tenants are created, updated or deleted.
