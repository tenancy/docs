---
title: Cache
icon: fal fa-window-restore
---

Currently the Laravel cache will use the same cache key for all tenants. 
This can and will cause issues in the long run. The config key for tenants 
should be automatically changed and the ability to disable overriding the cache key should be configurable inside `tenancy.php`.

The following example is an easy implementation to allow per-tenant prefixes with spatie permissions package in mind.

First turn off auto-discovery for hyn/multi-tenant (if you are using spatie permissions then turn off for that package also)

```json
"extra": {
        "laravel": {
            "dont-discover": [
              "spatie/laravel-permission",
              "hyn/multi-tenant"
            ]
        }
    },
```

Now run `composer update` with `php artisan package:discover`.

Add a `TenancyCacheServiceProvider` with these contents 
(in this example `app/Providers/TenancyCacheServiceProvider.php`):

```php
<?php

namespace App\Providers;
use Illuminate\Cache\CacheServiceProvider;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Cache;
use Illuminate\Cache\RedisStore;

class TenancyCacheServiceProvider extends CacheServiceProvider
{
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
}
```

This does not require a new Cache driver, as it's re-using 
the existing Redis driver. All this does is override the default prefix and 
allows you to set a custom cache driver in `config/cache.php`:

> Make sure to run `composer update` and `php artisan package:discover` before this step or else you will get "redis_tenancy driver not found error

```php
'redis' => [
    'driver'     => 'redis_tenancy',
    'connection' => 'default',
],
```

Now manually add hyn/multi-tenant and/or spatie/laravel-permission providers to your app.php under 'providers'  
Make sure to disable Laravel's CacheServiceProvider as TenancyCacheServiceProvider extends that one.

```php
'providers' => [
    //Tenant aware redis cache
    App\Providers\TenancyCacheServiceProvider::class,
    // Hyn multi tenancy.
    Hyn\Tenancy\Providers\TenancyProvider::class,
    Hyn\Tenancy\Providers\WebserverProvider::class,
    Spatie\Permission\PermissionServiceProvider::class, //add this if you have Spatie Permissions package
    ...
    #Illuminate\Cache\CacheServiceProvider::class, //this is now disabled
    ...
],
```

Now you can use CACHE and SESSION redis drivers

