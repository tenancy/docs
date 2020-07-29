---
title: Migrations and seeds
icon: fal fa-inventory
excerpt: >
    Automatically run migrations and seeds on tenant
    lifecycle events.
tags:
    - hooks
    - tenant
    - migrations
    - seeds
---

# Lifecycle Hook: Migrations

- [Overview](#overview)
- [Installation](#installation)
- [Migrations](#migrations)
  - [Configuration](#migrations-configuration)
  - [Example](#migrations-example)
- [Seeds](#seeds)
  - [Configuration](#seeds-configuration)
  - [Example](#seeds-example)

## Overview

**Purpose**

The purpose of this package is to handle the running of migrations and seeds.

**Requirements**

This package requires the installation and configuration of the [`affects-connections`](https://tenancy.dev/docs/tenancy/1.x/affects-connections) package. 

**Use Cases**

- Run migrations after a database is created
- Run seeds after the database is created
- Run migrations after a tenant is updated or deleted.
- and Many More!

**Events & Methods**

- ​	`Tenancy\Hooks\Migration\Events\ConfigureMigrations`
  - `path()`
  - `disable()`
  - `priority()`
- `Tenancy\Hooks\Migration\Events\ConfigureSeeds`
  - `seed()`
  - `disable()`
  - `priority()`

## Installation

`hooks-migration` is a package that is a bit more complex than most Tenancy packages. This will take care of everything after a Database is created for your multi-database setup.

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/hooks-migration
```

### Using Tenancy/Tenancy or with provider discovery disabled
Register the following ServiceProvider: 
  - `Tenancy\Hooks\Migration\Provider::class`

## Migrations

### Migrations Configuration

Once you've installed the package, you will have to configure how Tenant should be migrated. You can do this by listening to the `\Tenancy\Hooks\Migration\Events\ConfigureMigrations` event. It will provide you with some simple functionality in order to make your Migrations work like a charm:
- `path()`, this function allows you to register a path where your migrations are located. These migrations will be used once the migrations run.
- `disable()`, this function allows you to completely disable the migrations.
- `priority()`, this function allows you to change the priority in which the migrations run.

> Warning: The default priority for migrations is: `-50` and will run *after* the [Database Hooks](hooks-database).
>
> If migrations are not disabled or ran with a priority less than `-100` for the `Deleted` event you will receive an error as the database will have already been deleted.

### Migrations Example
In the example below we'll add the `database/tenant/migrations` folder containing migrations that should be used for the Tenant database.

```php
namespace App\Listeners;

use Tenancy\Hooks\Migration\Events\ConfigureMigrations;

class ConfigureTenantMigrations
{
    public function handle(ConfigureMigrations $event)
    {
        $event->path(database_path('tenant/migrations'));
    }
}
```

## Seeds

### Seeds Configuration

Once you've installed the migrations, you might want to seed the database with some data for the tenant. In that case you can listen to the `\Tenancy\Hooks\Migration\Events\ConfigureSeeds` event. This event has one very simple method that will be of use to you:
- `seed()`, this function will take the string of the seeder class. You can see how to use this in the example below.
- `disable()`, this function allows you to completely disable the seeds.
- `priority()`, this function allows you to change the priority in which the seeds run.

> Note: the default priority is `-40` and will default to running after the migrations hook.

### Seeds Example

```php
namespace App\Listeners;

use Tenancy\Hooks\Migration\Events\ConfigureSeeds;

class ConfigureTenantSeeds
{
    public function handle(ConfigureSeeds $event)
    {
        $event->seed(\UsersSeeder::class);
    }
}
```
