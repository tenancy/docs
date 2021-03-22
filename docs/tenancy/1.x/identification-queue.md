---
title: Identification driver queue
icon: fal fa-conveyor-belt
excerpt: >
    Identifies tenants using the queue.
tags:
    - identification
    - console
    - driver
    - queue
---

# Tenant Identification: Queue

- [Overview](#overview)
- [Introduction](#introduction)
- [Installation](#installation)
- [Configuration](#configuration)
- [Example](#example)
- [Overriding](#overriding)
  - [Example Job](#example-job)

## Overview

**Purpose**

The purpose of this package is to allow a [Tenant](tenant-what-is) to be identified in a queued Job.

**Requirements**

The Tenant [must be registered in the TenantResolver](tenant-setup)

**Use Cases**

- Identify the Tenant a specific job is for
- And many more!

## Introduction

One of the most powerful features in Laravel are the [queues](https://laravel.com/docs/master/queues). These are used for running specific tasks that don't need a UI and can be delayed. These can be hard to handle when it comes to multi-tenancy, and that is exactly where this package comes in.

The package already does a lot of work for you, if an tenant is identified at the moment a job is queued, it will remember which tenant it was. It will also provide you with some powerful override tools in case you want to queue something for a different tenant.

## Installation

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/identification-driver-queue
```

### Using Tenancy/Tenancy or with provider discovery disabled
Register the following ServiceProvider: 
  - `Tenancy\Identification\Drivers\Queue\Providers\IdentificationProvider::class`

## Configuring
In order to enable queue identification for a tenant, it will have to implement the specific contract `Tenancy\Identification\Drivers\Queue\Contracts\IdentifiesByQueue`. You will see that in this function you will get a simple custom `Processing` event which will contain one of these 2 pieces of information:
- The default provided `tenant_key` and `tenant_identifier` that are registered if a tenant was identified when a job was queued.
- The [overridden](#overriding) `tenant`, `tenant_key` or `tenant_identifier`, which you can provide yourself.

### Example
In the example below we will first check if an override tenant has been provided. If that is not the case, we will simply check if we can find the tenant in this model.

`app/Models/Customer.php`
```php
use Illuminate\Database\Eloquent\Model;
use Tenancy\Identification\Concerns\AllowsTenantIdentification;
use Tenancy\Identification\Contracts\Tenant;
use Tenancy\Identification\Drivers\Queue\Events\Processing;
use Tenancy\Identification\Drivers\Queue\Contracts\IdentifiesByQueue;

class Customer extends Model implements Tenant, IdentifiesByQueue
{
    public function tenantIdentificationByQueue(Processing $event): ?Tenant
    {
        if ($event->tenant) {
            return $event->tenant;
        }

        if ($event->tenant_key == $this->getTenantKey() && $event->tenant_identifier === $this->getTenantIdentifier()) {
            return $this->newQuery()
                ->where($this->getTenantKeyName(), $event->tenant_key)
                ->first();
        }

        return null;
    }
} 
```

## Overriding

Sometimes you might want to override the tenant (when you have an admin panel that is a tenant for example). You can do this by providing one of the following public keys for a job:

- `tenant_identifier`, this should be the same as the `getTenantIdentifier` of a tenant.
- `tenant_key`, this should be the same as the `getTenantKey` of a tenant.
- `tenant`, this can be an entire Tenant object.

### Overridable Job

```php
namespace App\Jobs;

use App\Models\Customer;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Tenancy\Facades\Tenancy;

class MailCustomer implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    /** @var Customer $tenant */
    public $tenant;

    public function __construct(Customer $customer, $tenant_key = null, string $tenant_identifier = null)
    {
        $this->tenant = $customer;
    }

    public function handle()
    {
        // ..
    }
}

```
