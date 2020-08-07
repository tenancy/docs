---
title: Hooks Database
icon: fal fa-database
excerpt: >
    How tenancy works with the Tenant lifecycle for Database mutations.
tags:
    - hooks
    - tenant
    - database
---
# Lifecycle Hook: Database

1. [Overview](#overview)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [Configuring](#configuring)
   1. [Example](#configuring-example)
   2. [Remote Database Servers](#remote-database-servers)
   3. [Seperate Database Server](#seperate-database-servers)
5. [Database Mutations](#database-mutations)
   1. [Example](#mutations-example)

## Overview

**Purpose**

The purpose of this package is to handle the creation, updating, and deletion of a Tenant's Database.

**Requirements**

In order to use this package a [Database Driver](database-drivers) must also be installed.

**Recommendations**

- To use the Tenant Database with the `onTenant` trait; The [`affects-connections`](affects-connections) package should also be installed.

**Use Cases**

- Create a new Database when a Tenant is created.
- Update the Database when the Tenant is updated.
- Delete a Database when a Tenant is deleted.
- and Many More!

**Events & Methods**

- `Tenancy\Hooks\Database\Events\Drivers\Configuring`
    - `useConnection()`
    - `useConfig()`
    - `defaults()` 
-  `Tenancy\Hooks\Database\Events\ConfigureDatabaseMutation`
    - `disable()`
    - `priority()`

## Requirements

A Database Driver is responsible for actually creating the database. Most of the Database Drivers will run specific "elevated permissions" queries in order to provide a database and/or a database user.

Find a driver and corresponding information on the [Database Drivers Page](database-drivers)

## Installation

Most hooks have a really straight forward installation. `hooks-database` has a different approach and requires at least one of the [Database Drivers](database-drivers) to also be installed.

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/hooks-database
```

### Using Tenancy/Tenancy or with provider discovery disabled
Register the following ServiceProvider: 
  - `Tenancy\Hooks\Database\Provider::class`

## Configuring

After requiring the package, we highly recommend you to listen to the `Tenancy\Hooks\Database\Events\Drivers\Configuring` event. This allows you to adjust the Database configuration just the way you want it.

There are a few functions to help you with setting up your Configuration.

- `useConnection()`, this function allows you to use one of the connections that you have defined in the `config/database.php` of your Laravel application. It uses the name of the connection to identify which connection you're trying to use.
- `useConfig()`, this function allows you to use a file in any path to provide an array as a configuration.

> All the above functions allow you to provide an `override` which will be merged in the provided configuration.

- `defaults()`, this will return an array that uses Tenancy's internal code to figure out the following:
  - Database Name, which is the tenant key
  - Database User, which is the tenant key
  - Database User Password, which is generated based on the `PasswordGenerator` that is in `tenancy/framework`.

### Configuring Example

In the example below we will configure the database to be created using the information from the `mysql` database connection defined in the `config/database.php` and add Tenancy's default database settings to this.

```php
namespace App\Listeners;

use Tenancy\Hooks\Database\Events\Drivers\Configuring;

class ConfigureTenantDatabase
{
    public function handle(Configuring $event)
    {
        $event->useConnection('mysql', $event->defaults($event->tenant));
    }
}
```

> Note: The `Tenancy\Hooks\Database\Events\Drivers\Configuring` event is only used to generate the
statements required to manage the Tenant's database and user. It is *NOT* used to connect to the
database that these statements will run on.

#### Remote Database Servers
In many production applications, the database will be on a seperate server than the application
is executing on. This may cause issues in some cases due to how databases configure user access.

Expanding on the previous example, the example below will set the host for the user to a wildcard
allowing this user to connect from any host and access the database.

```php
namespace App\Listeners;

use Tenancy\Hooks\Database\Events\Drivers\Configuring;

class ConfigureTenantDatabase
{
    public function handle(Configuring $event)
    {
        $overrides = array_merge(
            ['host'=>'%'],
            $event->defaults($event->tenant)
        );
        $event->useConnection('mysql', $overrides);
    }
}
```

#### Seperate Database Servers
By default Tenancy will try to create the Tenant's database and user using the default connection.
In cases where Tenant's are stored on different database servers you will need implement the
`ManagesSystemConnection` interface.

To get started have the [Tenant](what-is-a-tenant) class implement the `ManagesSystemConnection` interface
and implement the `getManagingSystemConnection` method.

In the following example, we assume that you have a a `premium-mysql` connection specified in your
`config/database.php` configuration file.

```php
use Illuminate\Database\Eloquent\Model;
use Tenancy\Identification\Concerns\AllowsTenantIdentification;
use Tenancy\Identification\Contracts\Tenant;
use Tenancy\Database\Drivers\Mysql\Concerns\ManagesSystemConnection;

class User extends Model implements Tenant, ManagesSystemConnection
{
    use AllowsTenantIdentification;

    public function getManagingSystemConnection(): ?string
    {
        return 'premium-mysql';
    }
}
```

## Database Mutations

Tenancy has provided a specific Event (`Tenancy\Hooks\Database\Events\ConfigureDatabaseMutation`) that will allow you to "configure" the Database Mutation. This event will be fired once we're sure that the Database Mutation should fire.

This event will allow you to:

- Reprioritize the Hook
- Disable the hook

You could do anything you could want when this event is fired.

### Mutations Example

In the example below we disable the mutation in order to retain the database after a Tenant is deleted.

```php
namespace App\Listeners;

use Tenancy\Hooks\Database\Events\ConfigureDatabaseMutation;
use Tenancy\Tenant\Events\Deleted;

class ConfigureTenantDatabaseMutations
{
    public function handle(ConfigureDatabaseMutation $event)
    {
        if($event->event instanceof Deleted)
        {
            $event->disable();
        }
    }
}
```

