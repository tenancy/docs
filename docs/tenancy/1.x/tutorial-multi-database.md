---
title: Multi Database Setup
icon: fal fa-database
excerpt: >
    How to create a Multi Database Setup with the Tenancy Ecosystem
tags:
    - tenancy
    - tutorial
    - multi database
---

## Introduction
Setting up a multi database tenancy setup can be quiet difficult. There's a lot of different (moving) pieces and that's exactly why we made this tutorial. In this tutorial we will talk about:
- Setting up tenancy so it will create a new DB for each tenant
- Changing the connection whenever a tenant is identified

> This tutorial assumes that you have a basic setup (meaning a tenant model that is firing hooks).

## Creation of the database
Creating of the database is a collaboration between 2 different type of packages:
- A Lifecycle Hook (`tenancy/hooks-database` in this case)
- A Database Driver (`tenancy/db-driver-mysql` for example). There are more Database Driver to choose from, in case you need one for a different Database.

### Setting Up
We will first focus on installing `hooks-database`. You can install this package by simply running the following command in your project:
```bash
composer require tenancy/hooks-database
```

After that is done, we will focus on configuring the creation of the database.

Configuring the database is fairly easy. It starts of with creating a new listener that listens to the `Tenancy\Hooks\Database\Events\Drivers\Configuring` event. Once you've created a listener, we will go to the actual configuration.

There are multiple ways to configure the Database Creation. In this tutorial we will use tenancy's default functionality in order to make it work. In your listener use the following code:
```php
$event->useConnection('mysql', $event->defaults($event->tenant))
```

What this code will do is quite simple:
- It will use the `mysql` configuration provided in the `config/database.php` of your application.
- It will use the `tenant_key` for a database name and database username
- It will use some information on the tenant for generating a secret password

You've now completely configured the creating of the database, but now it's time to actually create the database.

Creating the database is really easy, simply install the database driver of your choice (we recommend `tenancy/db-driver-mysql`) through composer.

You should now be able to create a new Database, by simply creating a new tenant.

## Changing the connection
There's one package responsible for changing of the connection and that is `affects-connections`. Once a tenant is identified, it will creating a new `tenant` connection which will direct to the database that we setup earlier. So let's get started on this installation.

First, install the package by running:
```bash
composer require tenancy/affects-connections
```

After the installation, we will focusing on `Resolving` the connection. This is simply us telling tenancy what class it should use in order to get a configuration for the connection. Create a new listener that listens to the `Tenancy\Affects\Connections\Events\Resolving` event. 

> Important: This event expects to **return** an instance/class that will provide the configuration of the connection, not the configuration itself.

In this example, we will tell tenancy that this listener is responsible for configuring that connection. We do this by simply returning the instance like shown below.

```php
<?php

namespace App\Listeners;

use Tenancy\Affects\Connections\Events\Resolving;

class ResolveTenantConnection
{
    public function handle(Resolving $event)
    {
        return $this;
    }
}
```

However, this class does not implements the `ProvidesConfiguration` contract which is responsible for providing a connection configuration to Tenant, so we will do that.

```php
<?php

namespace App\Listeners;

use Tenancy\Identification\Contracts\Tenant;
use Tenancy\Affects\Connections\Contracts\ProvidesConfiguration;
use Tenancy\Affects\Connections\Events\Resolving;

class ResolveTenantConnection implements ProvidesConfiguration
{
    public function handle(Resolving $event)
    {
        return $this;
    }

    public function configure(Tenant $tenant): array
    {
        return [];
    }
}
```
Right now we're providing an empty array as a connection setting. This won't work, and we will have to implement some logic in order to provide an actual connection configuration. You can do 2 different types of setups in this case:
- You can put all the logic for a connection here.
- You can fire off a `Configuring` event in order to configure it in a different listener.

In this tutorial we decided to fire off a `Configuring` event. You can do that by looking at the example below.
```php
<?php

namespace App\Listeners;

use Tenancy\Identification\Contracts\Tenant;
use Tenancy\Affects\Connections\Events\Resolving;
use Tenancy\Affects\Connections\Events\Drivers\Configuring;
use Tenancy\Affects\Connections\Contracts\ProvidesConfiguration;

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

After you've done this, we will move to the actual configuring of the connection. Create a new listener that listens to the `Tenancy\Affects\Connections\Events\Drivers\Configuring` event. This event is basically the same event as the event we used for the database.
This allows us to use the exact same code that we used then in order to configure the database. You can see an example below.

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

## Done!
You're all done now, congratulations! You've now setup your own multi tenancy setup using the Tenancy Ecosystem!

You should have the following result:
- Creating a new Customer (or your own tenant model), will result in a new database creation
- Switching to the tenant will create a new `tenant` connection.
