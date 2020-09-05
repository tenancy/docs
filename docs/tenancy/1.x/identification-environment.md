---
title: Environment Identification
icon: fal fa-leaf
excerpt: >
    Identifies tenants using the environment
tags:
    - environment
    - console
    - driver
    - artisan
---

# Tenant Identification: Environment

- [Overview](#overview)
- [Introduction](#introduction)
- [Installation](#installation)
- [Configuration](#configuration)
  - [Example](#example)

## Overview

**Purpose**

The purpose of this package is to allow a [Tenant](tenant-what-is) to be identified based on the environment

**Requirements**

The Tenant [must be registered in the TenantResolver](tenant-setup)

**Use Cases**

- Identify a Tenant by which environment the application is running in
- Ensure a specific tenant is loaded in a demonstration environment
- And many more!

## Introduction

This package allows the identification of a tenant based on the environment.

## Installation

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/identification-driver-environment
```

### Using Tenancy/Tenancy or with provider discovery disabled
Register the following ServiceProvider: 
  - `Tenancy\Identification\Drivers\Environment\Providers\IdentificationProvider::class`

## Configuration

This allows you to set up your own identification requirements based on environment variables, eg:

* Slug, custom generated, human readable string.
* Id, the database auto increment id.

### Example
```php
use Illuminate\Database\Eloquent\Model;
use Tenancy\Identification\Concerns\AllowsTenantIdentification;
use Tenancy\Identification\Contracts\Tenant;
use Tenancy\Identification\Drivers\Environment\Contracts\IdentifiesByEnvironment;

class Customer extends Model implements Tenant, IdentifiesByEnvironment
{
  use AllowsTenantIdentification;
  
    /**
     * Specify whether the tenant model is matching the request.
     *
     * @return Tenant
     */
    public function tenantIdentificationByEnvironment(): ?Tenant
    {
        if ($tenant = env('TENANT')) {
            return $this->query()
                ->where('slug', $tenant)
                ->first();
        }
        
        return null;
    }
}
```

> Be warned that using the `env()` helper will not work when the config is cached by Laravel.
