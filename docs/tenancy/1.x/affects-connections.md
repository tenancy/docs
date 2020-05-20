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

## Introduction
In some application you might want to completely separate tenant data by using different Databases, Database Servers, Clusters or anything like that. In that case what you looking for is `affects-connections`

## Installation
Install via composer
```bash
composer require tenancy/affects-connections
```

### Resolving the configuration
This package has quite a complex setup, but it's really worth it in the end.

We will start with the `Tenancy\Affects\Connections\Events\Resolving` event. This event allows you to provide an instance which will be used to Provide a configuration. You can do this by listening to the event and **returning** an instance that will provide the configuration for the upcoming connection request.

#### Example
In the below example we will return the instance itself as the class responsible for providing a connection configuration.
```php
<?php

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
- `useConnection`, this allows you to use a connection that you have already defined in the `config/database.php`
- `useConfig`, this allows you to use a connection that you have defined in a specific file.
> The functions listed above have an override parameter as a second parameter which allows you to override the configuration that they provide.
- `defaults`, this will return an array that uses Tenancy's internal code to figure out the following:
    - Database Name, which is the tenant key
    - Database User, which is the tenant key
    - Database User Password, which is a specially generated password based on multiple variables.

#### Example
In the following example we will use the mysql connection that is inside the `config/database.php` and combine it with the configuration that tenancy provides.
```php
<?php

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
After you've completely configured the connection, you can finally use and all of it's powers. The package has a few things to help you out with your most basic usage:

### Simple Trait
Tenancy has provided a very simply OnTenant trait (`Tenancy\Affects\Connections\Support\Traits\OnTenant`) which allows you to put models on the Tenant connection. You can see how to use it in the example below

```php
<?php

namespace App\Tenant\Models;

use Illuminate\Database\Eloquent\Model;
use Tenancy\Affects\Connections\Support\Traits\OnTenant;

class Role extends Model
{
    use OnTenant;
}
```
