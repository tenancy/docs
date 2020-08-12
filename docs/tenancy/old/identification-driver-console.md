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
need to apply a Contract to the [tenant][tenant-what-is] class and implement the required
methods.

This allows you to set up your own identification requirements, eg:

* Slug, custom generated, human readable string.
* Id, the database auto increment id.

## Usage

```php
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

[tenant-what-is]: tenant-what-is
