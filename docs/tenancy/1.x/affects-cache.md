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

# Affects-Cache

1. [Overview](#overview)
2. [Installation](#installation)
3. [Configuration](#configuration)
    1. [Example](#example)

## Overview

**Purpose**

The purpose of this package is to allow the use of different cache drivers and settings based on the tenant.

**Use Cases**

- Store cache information in different locations per tenant
- Store cache files for each tenant on their own database.

**Events & Methods**

- `Tenancy\Affects\Cache\Events\ConfigureCache`

## Installation
Install via composer
```bash
composer require tenancy/affects-cache
```

## Configuration
Configuration of the package is really easy. You can listen to the `Tenancy\Affects\Cache\Events\ConfigureCache` event to start configuring. Configuration can then be done by simply changing the config that will automatically be considered the `tenant` cache driver.

### Example
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