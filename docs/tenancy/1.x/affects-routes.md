---
title: Routes
icon: fal fa-reply-all
excerpt: >
    How you can easily affect the routes in your Laravel application.
tags:
    - affects
    - tenant
    - routes
---

## Introduction
In order to keep your application clean, you might want to separate the routes for tenants into different files.

## Installation
Install via composer:
```bash
composer require tenancy/affects-routes 
```

## Configuring
After the installation of the package, all you have to do is configure the package in the way you want. Like most affects, this package will fire an event `Tenancy\Affects\Routes\Events\ConfigureRoutes`, which will provide you with some functionality to load and configure your routes:
- `flush()`, this will remove all currently loaded routes.
- `fromFile()`, this will allow you to load routes from a specific file.

## Example
In the example we will register the routes for a Tenant if one is identified.
```php
<?php

namespace App\Listeners;

use Tenancy\Affects\Routes\Events\ConfiguresRoutes;

class TenantRoutes 
{
    public function handle(ConfiguresRoutes $event) 
    {
        if($event->event->tenant)
        {
            $event
                ->flush()
                ->fromFile(
                    ['middleware' => ['web']],
                    base_path('/routes/tenant.php')
                );
        }
    }
}
```
