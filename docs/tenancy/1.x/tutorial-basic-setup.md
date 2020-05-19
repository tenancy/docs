---
title: Basic Setup
icon: fal fa-cogs
excerpt: >
    How to create a basic Setup with the Tenancy Ecosystem
tags:
    - tenancy
    - tutorial
    - basic
---

## Introduction
The Tenancy Ecosystem works in really flexible ways. In order for you to get started easily we will show you how to create a really basic setup. In this basic setup we will teach you:
- How to create a tenant
- How to get tenancy ready for Identification
- How to get tenancy ready for Lifecycle Hooks

## Creating a tenant
After you've created your own Laravel project, we recommend you to start off by installing and configuring the `tenancy/framework`.

First, install the package:
```bash
composer require tenancy/framework
```

When the installation is done, create a model and a migration for your Tenant model. In this tutorial we will use a `Customer` model.

After you've created your model, make sure it implements the `Tenancy\Identification\Contracts\Tenant` contract.
> Tip: You can easily implement the contract by using our simple trait: `Tenancy\Identification\Concerns\AllowsTenantIdentification`

Your model should now look something like this:
`app/Models/Customer.php`
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Tenancy\Identification\Concerns\AllowsTenantIdentification;
use Tenancy\Identification\Contracts\Tenant;

class Customer extends Model implements Tenant
{
    use AllowsTenantIdentification;
}
```

You've now succesfully created a simple tenant model!

## Preparing for Identification
Tenancy needs to be aware of the tenant that you have just created in order to identify it. We will do this by registering the model into the `TenantResolver` (which is simply a class responsible for identifying tenants).

We can easily register a model by using a callback in one of Laravel's default Service Providers. Let's use the `app/Providers/AppServiceProvider` in this case.

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use App\Models\Customer;
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
        $this->app->resolving(ResolvesTenants::class, function (ResolvesTenants $resolver){
            $resolver->addModel(Customer::class);
            return $resolver;
        })
    }
}
```

Now when the `TenantResolver` is being made, it will register the model. Really easy and really handy.
> Tip: If you have multiple Tenant models, you could register all of them like this.

## Preparing for Lifecycle Hooks
> If you have no idea what the Lifecycle or Lifecycle Hooks are, we recommend you to [read about those first](architecture-lifecycle.md).

To prepare your tenant for lifecycle hooks, we will fire off some events for when a model is:
- Created
- Updated
- Deleted

We can do this with Laravel's really handy `dispatchesEvents` variable on models. It allows you to define specific events that should be fired when a model is being made. Here's an example of how to make it work for our Customer model:
`app/Models/Customer.php`
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Tenancy\Identification\Concerns\AllowsTenantIdentification;
use Tenancy\Identification\Contracts\Tenant;
use Tenancy\Tenant\Events;

class Customer extends Model implements Tenant
{
    use AllowsTenantIdentification;

    protected $dispatchesEvents = [
        'created' => Events\Created::class,
        'updated' => Events\Updated::class,
        'deleted' => Events\Deleted::class,
    ];
}
```
Now when you're creating a model like you normally do, it will fire off the Events for tenancy. When Tenancy receives these events, it will start firing Lifecycle Hooks.
