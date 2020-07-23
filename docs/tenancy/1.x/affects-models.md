---
title: Models
icon: fal fa-hand-holding-box
excerpt: >
    How you can easily affect the models in your Laravel application.
tags:
    - affects
    - tenant
    - models
---

# Affects-Models

1. [Overview](#overview)
2. [Installation](#installation)
3. [Configuration](#configuration)
    1. [Example](#example)

## Overview

**Purpose**

The purpose of this package is to allow the modification of models provided by other packages

**Use Cases**

- Resolve specific tenant issues without going though logs for every tenant
- Find common problems across multiple tenants
- Log errors in different locations

**Events & Methods**

- `Tenancy\Affects\Models\Events\ConfigureModels`

> All the method calls will be forwarded to the model in a nice and simple way

## Installation
Install via composer
```bash
composer require tenancy/affects-models
```

## Configuring
After the installation, it's time for configuring. A lot of the setup for this package is the same as the other affects, but this one is a bit more flexible. It will fire the `Tenancy\Affects\Models\Events\ConfigureModels` event, which will forward all the method calls to the models in a nice and simple way.

You can do this by using the following functions:
- `$event->functionName($models, $parameter1, $parameter2, $parameterx ...)`, this will perform the function as normal functions on the models.
- `ConfigureModels::functionName($models, $paramater1, $parameter2, $parameterx ...)`, this will perform the functions as static functions on the models.

### Example
In the below example, we'll change the `ConnectionResolver` of the Models, so they will use a different Database Connection.

```php
namespace App\Listeners;

use Vendor\Package\Models\Permission;
use Tenancy\Tests\Mocks\ConnectionResolver;
use Tenancy\Affects\Models\Events\ConfigureModels;

class ConfigureTenantModels
{
    protected $model = Permission::class;

    public function handle(ConfigureModels $event)
    {
        if($event->event->tenant)
        {
            ConfigureModels::setConnectionResolver(
                $this->model,
                new ConnectionResolver('sqlite', resolve('db'))
            );
        }
    }
}
```