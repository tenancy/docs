---
title: Identification driver http
icon: fal fa-browser
excerpt: >
    Identifies tenants using the request (object).
tags:
    - identification
    - http
    - driver
---

## Introduction
Identifying a tenant through the HTTP request object is a very common way of identifying tenants.
In order to allow a tenant to be identified with the Http driver, you
need to apply a Contract to the [tenant](what-is-a-tenant) class and implement the required
methods.

This allows you to set up your own identification requirements, for example:
* Host, a Fully Qualified Domain Name.
* Path, the requested path in the URL.
* Query parameter.

Install using composer:

```bash
composer require tenancy/identification-driver-http
```

> Make sure that the model you are using [is registered in the TenantResolver](identification-general).

## Usage

```php
<?php

namespace App\Models;

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
portal hostname and path allow you to identify this Customer as being the Tenant for the current hostname and path 
requested.
