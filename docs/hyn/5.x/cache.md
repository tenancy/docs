---
title: Cache
icon: fal fa-window-restore
---

Currently the Laravel cache will use the same cache key for all tenants. This can and will cause issues in the long run. The config key for tenants should be automatically changed and the ability to disable overriding the cache key should be configurable inside tenancy.php

[@jeitnier](https://github.com/jeitnier) over at Github has an interesting solution and agreed to have it posted here in the documentation.

Added a `CacheServiceProvider` with these contents in boot method:

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

Doesn't require a new Cache driver, as it's just manually implementing the existing Redis driver. All this does is override the default prefix, which lets you do by setting a custom cache driver in `config/cache.php`.

```php
'redis' => [
    'driver'     => 'redis_tenancy',
    'connection' => 'default',
],
```

