---
title: Fallback
excerpt: Configure a landing page using different approaches
icon: fal fa-compass
---

There are several ways to organize a fallback domain. These are commonly used to offer
a landing page and/or admin area for your clients to configure their tenant website.

# Disabling abort to pass through

By default a 404 not found is served whenever auto identification is enabled and no
matching hostname entry was found in the system database.

You can disable this in the `tenancy.php` configuration file by setting the 
`abort-without-identified-hostname` option to `false`. This will prevent tenancy 
from throwing a 404 (not found) whenever it failed to identify a registered hostname.

Depending on how you set up your app, the code will now pass through to any registered routes.
For applications that have global routes defined for tenant specific functionality, you'll
run into the issue that no "tenant" connection was defined.

# Tenant routes override

Whenever you create a file `routes/tenants.php` and you have `tenancy.hostname.auto-identification`
enabled, all routes contained in that file will automatically override any declared global routes
from `routes/web.php` or `routes/api.php`. Make sure to apply the proper middleware to those routes.

In case you enable `tenancy.routes.replace-global` all previously declared routes
will be removed and replaced with the contents of the `routes/tenants.php` file.

> Understand unlike the default `web.php` and `api.php` files no group is set around this file. You need
to apply your own `namespace`, `middleware` and other group settings inside the `tenants.php`.

An example `routes/tenants.php` file:

```php
Route::middleware('web')
    ->namespace('App\\Http\\Controllers\\Tenant\\')
    ->group(function () 
{
    Route::get('/', 'HomeController');
});
```


# Routing with domain

One of the easiest solutions is using the `::domain()` option in routing called
[sub domain routing in Laravel][sub-domain-routing].

```php
Route::domain('master.your.app')->group(function () {
    // .. your landing page routes
});
```

# Create a tenant as fallback

Another quick method is to simply seed a default tenant that has a specific
hostname. Set the environment `TENANCY_DEFAULT_HOSTNAME` to the exact FQDN used
for that tenant.

Now every time no other tenant was identified, it will revert to this tenant. A
drawback of this method is the need to set up a tenant hostname.

[sub-domain-routing]: https://laravel.com/docs/5.6/routing#route-group-sub-domain-routing
