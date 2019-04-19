---
title: Identification driver console
icon: fal fa-id-card-alt
excerpt: >
    Identifies tenants using the console/artisan.
tags:
    - identification
    - console
    - driver
    - artisan
---
Install using composer:

```bash
composer require tenancy/identification-driver-console
```

## Introduction

Uses the console input object to identify a tenant. 

In order to allow a tenant to be identified with the Console driver, all
commands have gained two new options, `--tenant` and `--tenant-type`.

For a tenant to be identified with the Console driver, you
need to apply a Contract to the [tenant][what-is-a-tenant] class and implement the required
methods.

This allows you to set up your own identification requirements, eg:

* Slug, custom generated, human readable string.
* Id, the database auto increment id.

## Usage

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Tenancy\Identification\Concerns\AllowsTenantIdentification;
use Tenancy\Identification\Contracts\Tenant;
use Tenancy\Identification\Drivers\Console\Contracts\IdentifiesByConsole;
use Symfony\Component\Console\Input\InputInterface;

class Customer extends Model implements Tenant, IdentifiesByConsole
{
  use AllowsTenantIdentification;
  
    /**
     * Specify whether the tenant model is matching the request.
     *
     * @param Request $request
     * @return Tenant
     */
    public function tenantIdentificationByConsole(InputInterface $input): ?Tenant
    {
        if (app()->runningInConsole() && $input->hasParameterOption('--tenant')) {
            return $this->query()
                ->where('slug', $input->getParameterOption('--tenant'))
                ->first();
        }
        
        return null;
    }
}
```

The example above assumes your Customer has a slug column with which we identify it.

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
            $resolver->addModel(Customer::class);
            
            return $resolver;
        });
    }
}
```

## Hostname model

To make integration easier, the package offers an example Hostname model you can use to make http identification
easier. In order to use this to identify a tenant you have to register the model as valid tenant in your
service provider:

```php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Tenancy\Environment;
use Tenancy\Identification\Drivers\Http\Models\Hostname;

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
        $this->app->make(Environment::class)
            ->getIdentificationResolver()
            ->addModel(Hostname::class);
    }
}
```

[what-is-a-tenant]: what-is-a-tenant
