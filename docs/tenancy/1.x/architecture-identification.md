---
title: About identification
icon: fal fa-id-card-alt
excerpt: >
    Identification process explained, including the drivers and configuration of each of these.
tags:
    - identification
    - tenant
    - driver
---
The tenant identification procedure decides which tenant you are trying to service. Although
using the TenantResolver isn't absolutely necessary,
doing so offloads a great deal of the tenancy business logic to the application.

The TenantResolver dispatches a few events used by the identification drivers to identify the
currently requested [tenant][what-is-a-tenant]. By using an event you are not limited to only 
one driver. Each driver will only trigger it's own contract when it is identifying. However, if identification is triggered without a specific driver, it will try to identify all drivers. The first driver to respond with a valid tenant object will cause any following
listeners to be ignored.

- [http](identification-http)
- [console](identification-console)
- [environment](identification-environment)
- [queue](identification-queue)

# General Principles
All types of identification that are using Tenancy's default `TenantResolver` require you to register Tenant model(s). Assuming
your tenant model is `App\Customer`, you could do so in the `AppServiceProvider` like this:

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

# Driver principles

Every driver uses the same principle. In order for a tenant to be used by the driver it has to
implement an interface (or in Laravel terminology contract) related to the driver. For instance
the http identification driver requires the tenant to implement the `IdentifiesByHttp` contract
which demands the method `tenantIdentificationByHttp`.

> The identification driver will only attempt identification of a tenant model when it implements
the contract of the driver.

[what-is-a-tenant]: what-is-a-tenant
