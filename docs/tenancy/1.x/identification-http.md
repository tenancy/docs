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

# Tenant Identification: HTTP

- [Overview](#overview)
- [Introduction](#introduction)
- [Installation](#installation)
- [Configuration](#configuration)
  - [Example](#example)
  - [Subdomain Example](#subdomain-example)

## Overview

**Purpose**

The purpose of this package is to allow a [Tenant](what-is-a-tenant) to be identified by a HTTP request.

**Requirements**

The Tenant [must be registered in the TenantResolver](identification-general)

**Use Cases**

- Identify a Tenant by a sub-directory path
- Identify a Tenant by a subdomain
- Identify a Tenant based on a unique domain
- Identify a Tenant based on a query parameter
- And many more!

## Introduction

Identifying a tenant through the HTTP request object is a very common way of identifying tenants.
In order to allow a tenant to be identified with the Http driver, you
need to apply a Contract to the [tenant](what-is-a-tenant) class and implement the required
methods.

## Installation

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/identification-driver-http
```

### Using Tenancy/Tenancy or with provider discovery disabled
Add the provider to `config/app.php`.

```php
    'providers' => [
        // ...
        
        /*
        * Package Service Providers...
        */
        Tenancy\Identification\Drivers\Http\Providers\IdentificationProvider::class,
        
        // ...
    ]
```

## Configuration

Tenants that will be identified via HTTP need to implement the `Tenancy\Identification\Drivers\Http\Contracts\IdentifiesByHttp` Contract.

The `tenantIdentificationByHttp` method should return the tenant that is identified based on the current request.

### Example

The example below assumes your Customer has two additional columns to identify the portal it's configured to use. The
portal hostname and path allow you to identify this Customer as being the Tenant for the current hostname and path 
requested.

```php
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

### Subdomain Example

The following example assumes that your Customer model has an extra column for a subdomain the user is configured to use.

```php
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
        list($subdomain) = explode('.', $request->getHost(), 2);
        return $this->query()
            ->where('subdomain', $subdomain)
            ->first();
    }
}
```

