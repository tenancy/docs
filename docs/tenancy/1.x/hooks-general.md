---
title: About lifecycle hooks
icon: fal fa-heartbeat
excerpt: >
    How tenancy hooks into tenant lifecycle events
tags:
    - hooks
    - tenant
---

# Lifecycle Hooks

- [Overview](#overview)
- [Events](#events)
- [Priority](#priority)
- [Custom Hooks](#custom-hooks)
- [Available Hooks](#available-hooks)

## Overview

Lifecycle hooks are executed whenever a tenant is created, updated or deleted.
They allow simple bootstrapping of a newly created tenant, like provisioning a new
database virtual machine, registering domains or setting up a S3 bucket.

When the `HookResolver` fires the hooks, it will:

 - Resolve the registered hooks.
 - Call the `for()` method on the hook with the specific [lifecycle event](#events).
 - Filter hooks that aren't supposed to fire, by checking the response of the method `fires()`.
 - Sorts based on the `priority()` method, see [priorities](#priorities).
 - Then calls the `fire()` method either:
   - directly.
   - or dispatched to the queue returned from the `queue()` method when `queued()` is true.

## Events

Lifecycle hooks listen for any of the following events:

- `Tenancy\Tenant\Events\Created` 
- `Tenancy\Tenant\Events\Updated` 
- `Tenancy\Tenant\Events\Deleted` 

You will have to fire these events in your own codebase, for instance:

```php
event(new \Tenancy\Tenant\Events\Created($tenant));
```

Or through Laravel's `$dispatchesEvents`:

```php
<?php
namespace App;

use Illuminate\Database\Eloquent\Model;

class Tenant extends Model
{
    protected $dispatchesEvents = [
        'created' => \Tenancy\Tenant\Events\Created::class,
        'updated' => \Tenancy\Tenant\Events\Updated::class,
        'deleted' => \Tenancy\Tenant\Events\Deleted::class,
    ];
}
```

Some pointers you need to take into consideration:

- The tenant has to implement the [Tenant Contract](identification-general#tenant-contract).
- Fire the event after your own code is finished.
- Fire the event outside of any transactions, so that changed attributes
have persisted and correct values are used by the hooks.

## Priorities

For hooks to be executed in the right sequence (eg migrations running after the database is created),
the hooks require a priority. Make sure your hooks use the correct value. Hooks are ran from lowest
to highest.

- Databases created, updated and deleted: `-100`
- Migrations, if hooks-migrations is enabled: `-50`

As a result if you want to set up and configure a database server when a tenant is created, make sure to
do so with a priority lower than -100. If you want to configure a database value dynamically after the
migrations and seeds are done, use a value higher than -50.

## Available Hooks

### First Party

[Database](hooks-database)

[Migrations](hooks-migrations)

[Hostname](hooks-hostname)