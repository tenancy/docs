---
title: Connections
icon: fal fa-network-wired
excerpt: >
    Makes the database connections adapt to your tenant identification.
tags:
    - affects
    - tenant
    - connections
---

# Affects-Connections

1. [Overview](#overview)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [Configuration](#configuration)
   1. [Resolving the Connection Configuration](#resolving-the-configuration)
      1. [Example](#resolving-example)
   2. [Configuring the Connection](#configuring-the-connection)
      1. [Example](#configuring-example)
5. [Using the Connection](#using-the-connection)
   1. [Tenancy's Trait Example](#trait-example)
   2. [Tenancy Facade Example](#tenancy-facade-example)
   3. [Laravel DB Facade Example](#laravel-db-facade-example)

## Overview

**Purpose**

The purpose of this package is to allow the access of Tenant data from a different data sources.

**Recommendations**

To automate the management of separate databases for each tenant install the [`hooks-database`](hooks-database) package.

**Use Cases**

- Tenant Data is stored in different Databases
- Tenant Data is stored on different Servers
- Tenant Data is stored in different Clusters
- and Many More!

**Events & Methods**

- `Tenancy\Affects\Connections\Events\Resolving`
- `Tenancy\Affects\Connections\Events\Drivers\Configuring`
  - `defaults`
  - `useConfig`
  - `useConnection`

## Installation

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/affects-connections
```

### Using Tenancy/Tenancy or with provider discovery disabled
Add the provider to `config/app.php`.

```php
    'providers' => [
        // ...
        
        /*
        * Package Service Providers...
        */
        Tenancy\Affects\Connections\Provider::class,
        
        // ...
    ]
```

## Configuration

This package has quite a complex setup, but it's really worth it in the end.

### Resolving the configuration

We will start with the `Tenancy\Affects\Connections\Events\Resolving` event. This event allows you to provide an instance which will be used to Provide a configuration. You can do this by listening to the event and **returning** an instance that will provide the configuration for the upcoming connection request.

#### Resolving Example
In the below example we will return the instance itself as the class responsible for providing a connection configuration.
```php
namespace App\Listeners;

use Tenancy\Affects\Connections\Contracts\ProvidesConfiguration;
use Tenancy\Affects\Connections\Events\Resolving;
use Tenancy\Affects\Connections\Events\Drivers\Configuring;

class ResolveTenantConnection implements ProvidesConfiguration
{
    public function handle(Resolving $event)
    {
        return $this;
    }

    public function configure(Tenant $tenant): array
    {
        $config = [];

        event(new Configuring($tenant, $config, $this));

        return $config;
    }
}
```

### Configuring the Connection
When the connection is resolved, we'll get to the configuring of the connection. In the example above we fire the `Configuring` event that is in the Tenancy package. We will now use that event to configure the connection. This event provides you with some functions in order to setup the database connection you want.
- `useConnection()`, this function allows you to use one of the connections that you have defined in the `config/database.php` of your Laravel application. It uses the name of the connection to identify which connection you're trying to use.
- `useConfig()`, this function allows you to use a file in any path to provide an array as a configuration.
> All the above functions allow you to provide an `override` which will be merged in the provided configuration.

- `defaults()`, this will return an array that uses Tenancy's internal code to figure out the following:
  - Database Name, which is the tenant key
  - Database User, which is the tenant key
  - Database User Password, which is generated based on the `PasswordGenerator` that is in `tenancy/framework`.

#### Configuring Example
In the following example we will use the MySQL connection that is inside the `config/database.php` and combine it with the configuration that tenancy provides.
```php
namespace App\Listeners;

use Tenancy\Affects\Connections\Events\Drivers\Configuring;

class ConfigureTenantConnection
{
    public function handle(Configuring $event)
    {
        $event->useConnection('mysql', $event->defaults($event->tenant));
    }
}
```

## Using the connection
After you've completely configured the connection, you can finally use and all of it's powers. The package creates a new connection named `tenant` which can be used as well as a simple trait to ensure Models use the Tenant's Database.

### Trait Example
The `OnTenant` trait (`Tenancy\Affects\Connections\Support\Traits\OnTenant`) allows you to put models on the Tenant connection. You can see how to use it in the example below.

```php
use Illuminate\Database\Eloquent\Model;
use Tenancy\Affects\Connections\Support\Traits\OnTenant;

class Role extends Model
{
    use OnTenant;
}
```

### Tenancy Facade Example

Using the `Tenancy` (`Tenancy\Facades\Tenancy`) Facade, we can also retrieve the connection of the identified tenant as seen in the example below.

```php
Tenancy::getTenantConnection()->select(...);
```

### Laravel DB Facade Example

If you are using Laravel's `DB` Facade, you are able to access the tenant connection as seen in the example below.

```php
DB::connection(Tenancy::getTenantConnectionName())->select(...);
```

> Note: This is not a recommended Method.