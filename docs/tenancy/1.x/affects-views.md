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

# Affects-Views

1. [Overview](#overview)
2. [Installation](#installation)
3. [Configuration](#configuration)
    1. [Example](#example)

## Overview

**Purpose**

The purpose of this package is to allow the use of custom views overriding or in addition to existing system views.

**Use Cases**

- Tenants require additional views customized for them
- Some tenants have additional feature sets that require additional views

**Events & Methods**

- `Tenancy\Affects\Views\Events\ConfigureViews`
  - `addNamespace`
  - `addPath`

## Introduction
Tenants might need different sites / views in your application. If you ever need such a thing, you can use `affects-views`. It will allow you to load and/or override views in our application really easily.

## Installation

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/affects-views
```

### Using Tenancy/Tenancy or with provider discovery disabled
Add the provider to `config/app.php`.

```php
    'providers' => [
        // ...
        
        /*
        * Package Service Providers...
        */
        Tenancy\Affects\Views\Provider::class,
        
        // ...
    ]
```

## Configuration
Once you've installed the package, all you have to do is configure it. The package will fire a `Tenancy\Affects\Views\Events\ConfigureViews` event to configure all the views for a specific tenant. The event will come with some basic functionality:
- `addNamespace()`, this will allow you to load a specific path of views in a specific namespace.
- `addPath()`, this will allow you to load a specific path of views into the specific namespace.

### Example
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
