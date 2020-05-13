---
title: Identification driver environment
icon: fal fa-id-card-alt
excerpt: >
    Identifies tenants using env variables.
tags:
    - identification
    - env
    - environment
    - driver
---
Install using composer:

```bash
composer require tenancy/identification-driver-environment
```

In order to allow a tenant to be identified with the Environment driver, you
need to apply a Contract to the [tenant](what-is-a-tenant) class and implement the required
methods.

This allows you to set up your own identification requirements based on environment variables, eg:

* Slug, custom generated, human readable string.
* Id, the database auto increment id.

## Usage

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


Make sure to register the model in the identification resolver:

```php
<?php

namespace App\Providers;

use App\Customer;
use Illuminate\Support\ServiceProvider;
use Tenancy\Identification\Contracts\ResolvesTenants;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        $this->app->resolving(ResolvesTenants::class, function (ResolvesTenants $resolver) {
            $resolver->addModel(Customer::class);
            
            return $resolver;
        });
    }
}
```
