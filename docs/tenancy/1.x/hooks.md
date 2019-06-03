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
 - Sorts based on the `priority()` method.
 - Then calls the `fire()` method either:
    - directly.
    - or dispatched to the queue returned from the `queue()` method when `queued()` is true.