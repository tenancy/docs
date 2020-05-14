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

# Installation
Most hooks have a really straight forward installation. `hooks-database` has a different approach and requires more work to actually get working. This hook actually requires atleast 2 different packages:
- `Hooks-Database`
- One of the Database Drivers

## Hook Database
This package has the main responsibility of providing an interface for providing the configuration of a Database and the actions around it.
```
composer require tenancy/hooks-database
```

### Setting up the Database Configuration
After requiring the package, we highly recommend you to listen to the `\Tenancy\Hooks\Database\Events\Drivers\Configuring` event. This allows you to adjust the Database configuration just the way you want it.

There are a few functions to help you with setting up your Configuration.
- `useConnection()`, this function allows you to use one of the connections that you have defined in the `config/database.php` in your Laravel application. It uses the name of the connection to identify which one you're trying to use.
- `useConfig()`, this function allows you to use a file in any path to provide an array as a configuration.
> All the above functions allow you to provide an `override` which will be merged in the provided configuration.
- `defaults()`, this function returns an array where tenant specific information and a password in inserted. The password is generated based on the `PasswordGenerator` that is in `tenancy/framework`.

## Database Driver
A Database Driver is responsible for actually creating the database. Most of the Database Drivers will run specific "elevated permissions" queries in order to provide a database and/or a database user.

There are some Database Drivers that are maintained by the Tenancy organisation, some might also be maintained by our Community.

Right now Tenancy provides several Database Drivers;
- SQLite
- MySQL

Each Database Driver will have their own installation guide.

# Advanced Setup

## ConfigureDatabaseMutation
Tenancy has provided a specific Event (`Tenancy\Hooks\Database\Events\ConfigureDatabaseMutation`) that will allow you to "configure" the Database Mutation. This event will be fired once we're sure that the Database Mutation should fire.

This event will allow you to:
- Reprioritize the Hook
- Disable the hook

You could do anything you could want when this event is fired.
