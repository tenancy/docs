---
title: Identification driver http
icon: fal fa-id-card-alt
excerpt: >
    Identifies tenants using the request (object).
tags:
    - identification
    - http
    - driver
---
Install using composer:

```bash
composer require tenancy/identification-driver-http
```

## Introduction

Uses the http request object to identify a tenant. 

In order to allow a tenant to be identified with the Http driver, you
need to apply a Contract to the [tenant][what-is-a-tenant] class and implement the required
methods.

This allows you to set up your own identification requirements, eg:

* Host, a Fully Qualified Domain Name.
* Path, the requested path in the URL.
* Get parameter.

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Http\Request;
use Tenancy\Identification\Concerns\AllowsTenantIdentification;
use Tenancy\Identification\Contracts\Tenant;
use Tenancy\Identification\Drivers\Http\Contracts\IdentifiesByHttp;

class Customer extends Model implements Tenant, IdentifiesByHttp
{
  use AllowsTenantIdentification;
  
    /**
     * Specify whether the tenant model is matching the request.
     *
     * @param Request $request
     * @return Tenant
     */
    public function tenantIdentificationByHttp(Request $request): ?Tenant
    {
        return $this->query()
            ->where('portal_hostname', $request->getHttpHost())
            ->where('portal_path', $request->path())
            ->first();
    }
}
```

The example above assumes your Customer has two additional columns to identify the portal it's configured to use. The
portal hostname and -path allow you to identify this Customer as being the Tenant for the current hostname and path 
requested.

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

## Eager identification

By default when the http identification is installed, a middleware is activated to identify tenants
as soon as possible. You can disable this by changing the configuration in the `identification-driver-http.php`
configuration file. It can be published using `php artisan vendor:publish --tag=identifiation-driver-http`.

> Instead of using tag `identification-driver-http` you can also use tag `tenancy` to publish all publishable files.

[what-is-a-tenant]: what-is-a-tenant
