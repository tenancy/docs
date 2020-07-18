---
title: Views
icon: fal fa-file-alt
excerpt: >
    How you can easily affect the views in your Laravel application.
tags:
    - affects
    - tenant
    - views
---

## Introduction
Tenants might need different sites / views in your application. If you ever need such a thing, you can use `affects-views`. It will allow you to load and/or override views in our application really easily.

## Installation
Install via composer
```bash
composer require tenancy/affects-views
```

### Configuring
Once you've installed the package, all you have to do is configure it. The package will fire a `Tenancy\Affects\Views\Events\ConfigureViews` event to configure all the views for a specific tenant. The event will come with some basic functionality:
- `addNamespace()`, this will allow you to load a specific path of views in a specific namespace.
- `addPath()`, this will allow you to load a specific path of views into the specific namespace.

## Example
In this example we'll load the tenant routes once a tenant is identified from the `resources/views/tenant` folder.
```php
namespace App\Listeners;

class ConfigureTenantViews
{
    public function handle(ConfigureViews $event)
    {
        if($event->event->tenant)
        {
            $event->addPath(resource_path('views/tenant'));
        }
    }
}
```
