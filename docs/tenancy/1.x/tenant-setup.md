---
title: Tenant Setup
icon: fal fa-id-card-alt
excerpt: >
    General tenant setup.
tags:
    - tenant
---

# Tenant Setup

- [Tenant Contract](#tenant-contract)
  - [Tenant Model Trait](#tenant-model-trait)
- [Tenant Registration](tenant-registration)
- [Next Steps](#next-steps)
  - [Lifecycle Hooks](#lifecycle-hooks)

## Tenant Contract

The tenant contract `Tenancy\Identification\Contracts\Tenant` marks a specific 
Class as a valid tenant. Have the class implement the contract methods to get started:

```php
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

### Tenant Model Trait

Of course tenancy offers an easy way of applying the methods
required by the contract to a model by using a trait for those specific functions.

```php
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

## Tenant Registration

In order for the package to know a valid tenant is available, you will need 
to register it on the tenant identification resolver. The best to do so is inside
your `app/Providers/AppServiceProvider` or alternatively a dedicated `app/Providers/TenantProvider`
which you created. You do this by providing the Tenant Resolver with the tenants using the `addModel` function.

```php
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

[Lifecycle Hooks](hooks-general) allow you to take specific actions when a Tenant is created, updated, or deleted.
