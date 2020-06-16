---
title: About lifecycle hooks
icon: fal fa-heartbeat
excerpt: >
    How tenancy hooks into tenant lifecycle events
tags:
    - hooks
    - tenant
---

Lifecycle hooks are executed whenever a tenant is created, updated or deleted.
They allow simple bootstrapping of a newly created tenant, like provisioning a new
database virtual machine, registering domains or setting up a S3 bucket.

## Events

Three events play a crucial role for Tenancy to do its magic, these are:

- `Tenancy\Tenant\Events\Created` 
- `Tenancy\Tenant\Events\Updated` 
- `Tenancy\Tenant\Events\Deleted` 

You will have to fire these events in your own codebase, for instance:

```php
event(new \Tenancy\Tenant\Events\Created($tenant));
```

Some pointers you need to take into consideration:

- The tenant has to be a [tenant](what-is-a-tenant).
- Fire the event after your own code is finished.
- Fire the event outside of any transactions, so that changed attributes
have persisted and correct values are used by the hooks.

## Hooks

You can build your own hook by implementing the `Tenancy\Contracts\LifecycleHook` contract.

There are two abstract Hook classes that make implementation easier:

- `Tenancy\Lifecycle\Hook`
- `Tenancy\Lifecycle\ConfigurableHook`

When the HookResolver fires the hooks it will:
 
 - Resolve the registered hooks.
 - Call the `for()` method on the hook with the specific [lifecycle event](#events).
 - Filter hooks that aren't supposed to fire, by checking the response of the method `fires()`.
 - Sorts based on the `priority()` method, see [priorities](#priorities).
 - Then calls the `fire()` method either:
    - directly.
    - or dispatched to the queue returned from the `queue()` method when `queued()` is true.
    
## Priorities

For hooks to be executed in the right sequence (eg migrations running after the database is created),
the hooks require a priority. Make sure your hooks use the correct value. Hooks are ran from lowest
to highest.

- Databases created, updated and deleted: `-100`
- Migrations, if hooks-migrations is enabled: `-50`

As a result if you want to set up and configure a database server when a tenant is created, make sure to
do so with a priority lower than -100. If you want to configure a database value dynamically after the
migrations and seeds are done, use a value higher than -50.

## Soft-Delete Models

When using a model that has soft-deletes enabled registering the lifecycle events in the model itself will cause the delete event to be thrown for both the "soft" and "hard" delete.

To overcome this you can create an [observer](https://laravel.com/docs/7.x/eloquent#observers) for your model and dispatch the appropriate events as needed. This will also allow you to preform other actions prior to the event being dispatched.

Here is an example of an observer.
```php
<?php

namespace App\Observers;

use App\User;

class UserObserver
{
    /**
     * Handle the user "created" event.
     *
     * @param  \App\User  $user
     * @return void
     */
    public function created(User $user)
    {
        event(new \Tenancy\Tenant\Events\Created($user));
    }

    /**
     * Handle the user "updated" event.
     *
     * @param  \App\User  $user
     * @return void
     */
    public function updated(User $user)
    {
        event(new \Tenancy\Tenant\Events\Updated($user));
    }

    /**
     * Handle the user "deleted" event.
     *
     * @param  \App\User  $user
     * @return void
     */
    public function deleted(User $user)
    {
        //Preform some logic when the model is "soft" deleted
    }

    /**
     * Handle the user "restored" event.
     *
     * @param  \App\User  $user
     * @return void
     */
    public function restored(User $user)
    {
        //Preform some logic when the model is restored after being "soft" deleted
    }

    /**
     * Handle the user "force deleted" event.
     *
     * @param  \App\User  $user
     * @return void
     */
    public function forceDeleted(User $user)
    {
        event(new \Tenancy\Tenant\Events\Deleted($user));
    }
}
```
