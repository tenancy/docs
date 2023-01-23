---
title: Cache
icon: fal fa-window-restore
---

Currently the Laravel cache will use the same cache key for all tenants.
This can and will cause issues in the long run. The config key for tenants
should be automatically changed and the ability to disable overriding the cache key should be configurable inside `tenancy.php`.

The following is an easy implementation to allow per-tenant prefixes.

Add a `CacheServiceProvider` with these contents in boot method:

```php
public function boot()
{
    Cache::extend('redis_tenancy', function ($app) {
        if (PHP_SAPI === 'cli') {
            $uuid = $app['config']['driver'];
        } else {
            // ok, this is basically a hack to set the redis cache store
            // prefix to the UUID of the current website being called
            $fqdn = request()->getHost();

            $uuid = DB::table('hostnames')
                ->select('websites.uuid')
                ->join('websites', 'hostnames.website_id', '=', 'websites.id')
                ->where('fqdn', $fqdn)
                ->value('uuid');
        }

        return Cache::repository(new RedisStore(
            $app['redis'],
            $uuid,
            $app['config']['cache.stores.redis.connection']
        ));
    });
}
```

This does not require a new Cache driver, as it's re-using
the existing Redis driver. All this does is override the default prefix and
allows you to set a custom cache driver in `config/cache.php`:

```php
'redis' => [
    'driver'     => 'redis_tenancy',
    'connection' => 'default',
],
```

> Make sure the CacheServiceProvider is registered in `config/app.php` or else your driver will 
not be available for use.

If you are using Spatie\Permission package and receive error about driver `redis_tenancy` is not supported,
you need to turn off Laravel's package auto discovery for the package. This way you can set the load order and
ensure that Spatie permissions is loaded after redis_tenancy driver has been added.

You can do this by adding don't discover to your composer.json file
```
"extra": {
   "laravel":
      "dont-discover": [
          "spatie/laravel-permission"
      ]
   }
}
```
Lastly make sure you load up the `PermissionServiceProvider` after your `AppServiceProvider` in `config/app.php`

```
App\Providers\AppServiceProvider::class,
...
Spatie\Permission\PermissionServiceProvider::class,
``` 

