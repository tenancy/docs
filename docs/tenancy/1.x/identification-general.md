---
title: General Setup
icon: fal fa-id-card-alt
excerpt: >
    General setup for tenant identification.
tags:
    - tenant
    - identification
---

# Tenant Identification

- [Overview](#overview)
- [Tenant Setup](#tenant-setup)
  - [Tenant Contract](#tenant-contract)
    - [Tenant Model Trait](#tenant-model-trait)
  - [Tenant Registration](tenant-registration)
- [Next Steps](#next-steps)
  - [Lifecycle Hooks](#lifecycle-hooks)
  - [Identification Drivers](#identification-drivers)

## Overview

The tenant identification procedure decides which tenant you are trying to service. Although using the `TenantResolver` isn't absolutely necessary, doing so offloads a great deal of the tenancy business logic to the application.

The `TenantResolver` dispatches a few events used by the identification drivers to identify the currently requested [Tenant](what-is-a-tenant). By using an event you are not limited to only one driver. Each driver will only trigger it's own contract when it is identifying. However, if identification is triggered without a specific driver, it will try to identify all drivers. The first driver to respond with a valid tenant object will cause the other drivers to be ignored.

> Before making any changes to any code, it is recommended that you read though this entire document first.

## Tenant Setup

### Tenant Contract

The tenant contract `Tenancy\Identification\Contracts\Tenant` marks a specific 
Class as a valid tenant. Have the class implement the contract methods to get started:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Tenancy\Identification\Contracts\Tenant;

class User extends Model implements Tenant
{
    /**
     * The attribute of the Model to use for the key.
     *
     * @return string
     */
    public function getTenantKeyName(): string
    {
        return 'id';
    }

    /**
     * The actual value of the key for the tenant Model.
     *
     * @return string|int
     */
    public function getTenantKey()
    {
        return $this->id;
    }

    /**
     * A unique identifier, eg class or table to distinguish this tenant Model.
     *
     * @return string
     */
    public function getTenantIdentifier(): string
    {
        return get_class($this);
    }
}
```

This will force your User model to implement some methods
required for tenancy to do its work. 

#### Tenant Model Trait

Of course tenancy offers an easy way of applying the methods
required by the contract to a model by using a trait for those specific functions.

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Tenancy\Identification\Concerns\AllowsTenantIdentification;
use Tenancy\Identification\Contracts\Tenant;

class User extends Model implements Tenant
{
    use AllowsTenantIdentification;
}
```

Your first valid tenant is a fact. Now we need to make sure our application
is aware it exists.

### Tenant Registration

In order for the package to know a valid tenant is available, you will need 
to register it on the tenant identification resolver. The best to do so is inside
your `app/Providers/AppServiceProvider` or alternatively a dedicated `app/Providers/TenantProvider`
which you created. You do this by providing the Tenant Resolver with the tenants using the `addModel` function.

```php
<?php

namespace App\Providers;

use App\User;
use Illuminate\Support\ServiceProvider;
use Tenancy\Identification\Contracts\ResolvesTenants;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        //
    }

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        $this->app->resolving(ResolvesTenants::class, function (ResolvesTenants $resolver) {
            $resolver->addModel(User::class);
            
            return $resolver;
        });
    }
}
```

## Next Steps

### Lifecycle Hooks

[Lifecycle Hooks](architecture-lifecycle) allow you to take specific actions when a Tenant is created, updated, or deleted.

### Identification Drivers

Every driver uses the same principle. In order for a tenant to be used by the driver it has to implement an interface (or in Laravel terminology contract) related to the driver.

Depending on your application, you may need to identify a Tenant using different methods. The following Identification Drivers are available to be used alone, or in combination.

[Console Identification](identification-console)

[Environment Identification](identification-environment)

[HTTP based Identification](identification-http)

[Queue Identification](identification-queue)

> The identification driver will only attempt identification of a tenant model when it implements the contract of the driver.