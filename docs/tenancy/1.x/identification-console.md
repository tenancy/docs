---
title: Console Identification
icon: fal fa-terminal
excerpt: >
    Identifies tenants using the console/artisan.
tags:
    - identification
    - console
    - driver
    - artisan
---

# Tenant Identification: Console

- Overview
- Introduction
- Installation
- Configuration
  - Example
- Usage

## Overview

**Purpose**

The purpose of this package is to allow a [Tenant](what-is-a-tenant) to be identified though console commands

**Requirements**

The Tenant Model [must be registered in the TenantResolver](identification-general)

**Use Cases**

- Identify a Tenant by a custom field
- Identify a Tenant by an ID
- And many more!

## Introduction

Uses the console input object to identify a tenant. 

In order to allow a tenant to be identified with the Console driver, all
commands have gained two new options, `--tenant` and `--tenant-identifier`.

For a tenant to be identified with the Console driver, you
need to apply a Contract to the [tenant][what-is-a-tenant] class and implement the required
methods.

## Installation
Install using composer:

```bash
composer require tenancy/identification-driver-console
```
> Make sure that the model you are using [is registered in the TenantResolver](identification-general).

## Configuration

Models that will be identified by console need to implement the `Tenancy\Identification\Drivers\Console\Contracts\IdentifiesByConsole` contract.

The `tenantIdentificationByConsole` method should return the tenant that is identified based on the current request.

### Example

The following example assumes your Customer has a slug column with which we use to identify it.

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

## Usage

### `--tenant`

In the following example we will assume that you want to list the routes available for a specific tenant.

We will also assume that the tenant in question has the previous example implemented, has the slug of "my-first-tenant".

```bash
php artisan route:list --tenant my-first-tenant
```

### `--tenant-identifier`

In the following example we will assume that you have a custom command, and in that command you do something to all tenants.

We will also assume that you have two Tenant models; User's and Organization's. 

In order to run your custom command only for User models you can do the following:

```bash
php artisan custom:command --tenant-identifier User
```

TODO: expand this example with detailed explanation 