---
title: General Setup
icon: fal fa-id-card-alt
excerpt: >
    General setup for tenant identification.
tags:
    - tenant
    - identification
---

## Registering Models
In order for the TenantResolver to be able to identify a tenant, it needs to know which Tenants it can choose from. You do this by providing the Tenant Resolver with the tenants using the `addModel` function.

In the below example, you can see how you can register a model.
```php
<?php

namespace App\Providers;

use App\Models\Customer;
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