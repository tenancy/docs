---
title: What is a tenant?
icon: fal fa-id-badge
excerpt: |
    How to determine the selection of your tenant, what are valid
    subjects and how do you configure these inside tenancy.
tags:
    - tenant
---

Whether to separate data based on the requested hostname, the logged
in user or the team the user belongs to, is entirely up to you.

The subject of tenancy, **the tenant**, can be any eloquent Model
in your application you wish to build your business logic around.

## Deciding on your tenant

The most important decision in building a multi tenant application
is choosing your tenant. In order to help you with this, ask yourself the
following questions.

To which entity in my business logic does the tenant data belong to?

Do I have companies with employees signing up? Then most likely that
company is the tenant, it holds all the information we want to keep separated
from any other company in the application. This is a very common
choice visible with applications using a vanity url, eg yourteam.saas.app.

Or perhaps we can keep it simple by marking the user as tenant. This will
connect all data to a single account instead. A great example of this is
GMail. You log in and see only the emails of your account.

A hybrid version is also well known, GitHub offers personal namespaces, for instance
github.com/luceos and team namespaces like github.com/tenancy.

Tenancy offers a solution for any scenario as there's no limitation to how
a tenant is identified, nor how many tenants can be configured. To give you an idea
using the hybrid example from above the tenants would be both a User model 
and an Organisation model. They can be marked as a valid tenant entity by applying
a contract and implementing the required methods. 

## Tenant model contract

The tenant contract `Tenancy\Identification\Contracts\Tenant` marks a specific 
model as a valid tenant. Have the model implement the contract methods to get started:

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

## Tenant model trait

Of course tenancy offers an easy way of applying the methods
required by the contract to the model by re-using Laravel
functionality:

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

Your first valid tenant model is a fact. Now we need to make sure our application
is aware it exists.

## Tenant model registration

In order for the package to know a valid tenant model is available, you will need 
to register it on the tenant identification resolver. The best to do so is inside
your `app/Providers/AppServiceProvider` or alternatively a dedicated `app/Providers/TenantProvider`
which you created.

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

