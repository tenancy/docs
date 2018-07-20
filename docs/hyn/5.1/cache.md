---
title: Cache
icon: fal fa-window-restore
---

> If you want to use the custom cache driver, you should disable package auto discovery for this package and register the package provider `\Hyn\Tenancy\Providers\TenancyProvider::class` to `config/app.app` after your own cache provider.

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
            $fqdn = $_SERVER['SERVER_NAME'];
            
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

> Make sure the CacheServiceProvider is registered in `config/app.php`. Or your driver will not be
available for use.
