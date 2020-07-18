---
title: Cache
icon: fal fa-inventory
excerpt: >
    Makes the cache adapt to your tenant identification.
tags:
    - affects
    - tenant
    - cache
---

## Introductio
One of the most important parts in an application is the cache. It is a major performance boost of websites. You would want your cache to be tenant aware in a lot of cases.

## Installation
Install via composer
```bash
composer require tenancy/affects-cache
```

## Configuring
Configuration of the package is really easy. You can listen to the `Tenancy\Affects\Cache\Events\ConfigureCache` event to start configuring. Configuration can then be done by simply changing the config that will automatically be considered the `tenant` cache driver.

## Example
In the below example, we will add a `tenant` cache driver that uses the `tenant` database connection.
```php
namespace App\Listeners;

use Tenancy\Affects\Cache\Events\ConfigureCache;

class ConfigureTenantCache
{
    public function handle(ConfigureCache $event)
    {
        $event->config = [
            'driver' => 'database',
            'table' => 'cache',
            'connection' => 'tenant'
        ];
    }
}
```