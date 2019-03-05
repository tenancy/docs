---
title: Connections
icon: fal fa-warehouse-alt
---

# Database Connections

One of the core aspects of tenancy is the database connection. Tenancy
gives you access to two key database connections; `tenant` and `system`.

## System Connection
Out of the box, the package gives you a `system` database connection which acts as
the master database where `hostnames` and `websites` are stored.

If for some reason you need to create an Eloquent Model that would need to connect
to the system connection, you can apply the `Hyn\Tenancy\Traits\UsesSystemConnection`
trait on your given Model. In doing so, the model’s connection will always default to use the `system`.

One reason for using the `system` connection is when you have data which you'd like
to share between all tenants or which you need to influence the tenant identification
process. For instance when you are creating an admin portal that would manage all of your
tenants in one place.

## Tenant Connection
This is maybe the most used database connection. Data in use by single tenant instances,
not shared with others, is persisted into the database using the tenant connection. The
implementation of the tenant connection ensures that no tenant can access another tenants data.

By using the `Hyn\Tenancy\Traits\UsesTenantConnection` trait, the package will make sure
that it is using the right database connection.

All tenant related models should use the `UsesTenantConnection` trait.

### Forcing The Connection

If you want to set the default connection to `tenant` (when a tenant website has been identified), you can use the snippet below.
This is also helpful for 3rd party packages models to force their connection to `tenant`.

Add the following code to the `boot()` method of your `AppServiceProvider.php` file.

```php
$env = app(Environment::class);

if ($fqdn = optional($env->hostname())->fqdn) {
    config(['database.default' => 'tenant']);
}
```

> Please understand the risks involved of setting your default connection during runtime. All Eloquent models will from then on use the `tenant` connection instead of your previously configured default connection.

---

More information about applying these connections can be found in the [models](models) article.

### Password generation

The password of the tenant is generated using several components mashed up as md5 hash.

- `tenancy.key` with a fallback to `app.key`. You can specify an empty string `""` if you don't want to use this key.
- Website Id.
- Website UUID.
- Website created_at.

In case you wish to change/update the `tenancy.key` we added a useful command `tenancy:key:update`
which will update the tenant password with the new md5 value. This allows for easier key rotation.

