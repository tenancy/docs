---
title: Console Identification
icon: fal fa-id-card-alt
excerpt: >
    Identifies tenants using the console/artisan.
tags:
    - identification
    - console
    - driver
    - artisan
---

## Introduction

Uses the console input object to identify a tenant. 

In order to allow a tenant to be identified with the Console driver, all
commands have gained two new options, `--tenant` and `--tenant-type`.

For a tenant to be identified with the Console driver, you
need to apply a Contract to the [tenant][what-is-a-tenant] class and implement the required
methods.

This allows you to set up your own identification requirements, as an example:
* Slug, custom generated, human readable string.
* Id, the database auto increment id.

## Installation
Install using composer:

```bash
composer require tenancy/identification-driver-console
```
> Make sure that the model you are using [is registered in the TenantResolver](identification-general).

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
        if ($input->hasParameterOption('--tenant')) {
            return $this->query()
                ->where('slug', $input->getParameterOption('--tenant'))
                ->first();
        }
        
        return null;
    }
}
```

The example above assumes your Customer has a slug column with which we identify it.

[what-is-a-tenant]: what-is-a-tenant
