---
title: Environment Identification
icon: far fa-globe-africa
excerpt: >
    Identifies tenants using the environment
tags:
    - environment
    - console
    - driver
    - artisan
---

## Introduction
Sometimes you might want to do identification of a tenant based on the Environment the tenant/code is in.

Install using composer:

```bash
composer require tenancy/identification-driver-environment
```
> Make sure that the model you are using [is registered in the TenantResolver](identification-general).

This allows you to set up your own identification requirements based on environment variables, eg:

* Slug, custom generated, human readable string.
* Id, the database auto increment id.

## Example
```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Tenancy\Identification\Concerns\AllowsTenantIdentification;
use Tenancy\Identification\Contracts\Tenant;
use Tenancy\Identification\Drivers\Environment\Contracts\IdentifiesByEnvironment;

class Customer extends Model implements Tenant, IdentifiesByEnvironment
{
  use AllowsTenantIdentification;
  
    /**
     * Specify whether the tenant model is matching the request.
     *
     * @return Tenant
     */
    public function tenantIdentificationByEnvironment(): ?Tenant
    {
        if ($tenant = env('TENANT')) {
            return $this->query()
                ->where('slug', $tenant)
                ->first();
        }
        
        return null;
    }
}
```

> Be warned that using the `env()` helper will not work when the config is cached by Laravel.
