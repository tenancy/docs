---
title: Identification drivers
icon: fal fa-id-card-alt
excerpt: >
    Identification process explained, including the drivers and configuration of each of these.
tags:
    - identification
    - tenant
    - driver
---
The tenant identification procedure decides which tenant you are trying to service. Although
using the tenant resolver isn't absolutely necessary,
doing so offloads a great deal of the tenancy business logic to the package.

The tenant resolver dispatches a few events used by the identification drivers to identify the
currently requested [tenant][what-is-a-tenant]. By using an event you are not limited to only 
one driver. The first driver to respond with a valid tenant object will cause any following
listeners to be ignored.

## Http driver

Install using composer:

```bash
composer require tenancy/identification-driver-http
```

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

use Illuminate\Database\Eloquent\Model;
use Illuminate\Http\Request;
use Tenancy\Identification\Concerns\AllowsTenantIdentification;
use Tenancy\Identification\Contracts\Tenant;
use Tenancy\Identification\Drivers\Http\Contracts\IdentifiesByHttp;

class User extends Model implements Tenant, IdentifiesByHttp
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

The example above assumes your User has two additional columns to identify the portal it's configured to use. The
portal hostname and -path allow you to identify this User as being the Tenant for the current hostname and path 
requested.

## Console driver

@todo

## Environment driver

Install using composer:

```bash
composer require tenancy/identification-driver-environment
```

@todo

[what-is-a-tenant]: what-is-a-tenant
