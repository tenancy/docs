---
title: Custom lifecycle hooks
icon: fal fa-gear
excerpt: >
    How to create custom lifecycle hooks
tags:
    - hooks
    - tenant
---

# Lifecycle Hooks: Custom Hooks

- [Overview](#overview)
- [Base](#base)
- [Implementation](#implementation)
  - [General](#general)
  - [Differences](#differences)
- [Registration](#registration)
- [Advanced](#advanced)
  - [ConfiguresHook Event](#configureshook-event)

## Overview

**Purpose**

Enable custom functionality that isn't available through existing hooks.

**Requirements**

- Knowledge of how [Lifecycle Hooks work](hooks-general)
- A [Tenant](tenant-what-is) that dispatches the `Created`, `Updated`, and/or `Deleted` [lifecycle events](hooks-general#events).


## Base

You can build your own hook by implementing the `Tenancy\Contracts\LifecycleHook` contract.

There are two abstract Hook classes that make implementation easier:

- `Tenancy\Lifecycle\Hook`
- `Tenancy\Lifecycle\ConfigurableHook`

The basic difference is that the hook settings are not expected to change in a `Hook`,
but are expected to be fluid in the `ConfigurableHook`.

I.e. A Lifecycle Hook that always run at the same default priority on the same default queue should use `Hook`,
but a LifecycleHook that will run on the Tenant's Queue should use `ConfigurableHook`

## Implementation

### General

Implementation only requires you to create a `fire` method that contains the logic for the hook.

```php
public function fire()
{
    echo "Running hook for ".$this->event->tenant->name;
}
```

If the hook needs to implement different logic based on the event (I.e. Created vs Deleted) you can check the type of event in the `fire` method.

```php
public function fire()
{
    // Created Event
    if($this->event instanceof \Tenancy\Tenant\Events\Created)
    {
        dump("Running hook for the creation of a tenant");
        return;
    }
    // Deleted Event
    if($this->event instanceof \Tenancy\Tenant\Events\Deleted)
    {
        dump("Running hook for the deletion of a tenant");
        return;
    }
}
```

In the event the hook only needs to be run based on one event you can disable the hook and only enable it when it should be fired

```php
namespace App\Hooks;

class CustomCreateHook extends ConfigurableHook
{
    public $fires = false;

    public function for($event)
    {
        parent::for($event);
        
        if($this->event instanceof \Tenancy\Tenant\Events\Created)
        {
            $this->fires = true;
        }
        return $this;
    }
}
```

For more advanced or tenant-dependent changes to the event, consider creating an [Event and Listener](#configureshooks-event) to configure the Hook.

### Differences

The implementation of both `Hook` and `ConfigurableHook` is generally the same with the exception of the following differences.

**Queues**

`Hook` defaults to running on a queue, and defaults to the applications default queue.
`ConfigurableHook` does not default to using a queue and will only run on a queue when the queue is specified

```php
// Hook
public $queued = true;
public $queue = null;

// ConfigurableHook
public $queue = null;
```

**Fires**

`Hook` defaults to always fire as long as the event being passed is a valid Tenancy Event.
`ConfigurableHook` defaults to always fire.

```php
// Hook
public $event; //Must be an instance of Tenancy\Tenant\Events\Event
public $fires = true;

// ConfigurableHook
public $fires = true;
```

> Note: Both `Hook` and `ConfigurableHook` have the `$event` property which contains the tenant in `$event->tenant`

## Registration

There are two methods of registering your custom Lifecycle Event.

### Extend HooksProvider

The simplest method is to create a new Service Provider (or use an existing one) and have it extend `Tenancy\Support\HooksProvider`. Then create an array of your custom hooks.

```php
namespace App\Providers;

class TenantLifecycleProvider extends \Tenancy\Support\HooksProvider
{
    protected $hooks = [
        \App\Hooks\CustomHook::class,
    ];
}
```

> This is the recommended method of registering Lifecycle Events

### Resolving the Resolver

```php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Tenancy\Lifecycle\Contracts\ResolvesHooks;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        $this->app->resolving(ResolvesHooks::class, function (ResolvesHooks $resolver) {
            $resolver->addHook(\App\Hooks\CustomHook::class);
        });
    }
}
```

> Please note, this is not the recommended method of registering your Lifecycle Event

## Advanced

### ConfiguresHook Event

> It is recommended that you only implement this for `ConfigurableHook` hooks.

In the event you want to perform advanced logic to configure the hook, much like the available hooks, you can create a new Event and Listener in order to configure your custom Hook. In order to do so, ensure you override the `for` method in your hook as follows.

```php
// Hook
public function for($event)
{
    parent::for($event);
    event(\App\Events\ConfiguresCustomHookEvent($this->event, $this));
    return $this;
}
```

**Example Event**

```php
namespace App\Events;

class ConfiguresCustomHookEvent
{
    /**
     * @var Event
     */
    public $event;

    /**
     * @var ConfigurableHook
     */
    public $hook;

    public function __construct(Event $event, ConfigurableHook $hook)
    {
        $this->event = $event;
        $this->hook = $hook;
    }
    
    public function disable()
    {
        $this->hook->fires = false;
        return $this;
    }

    public function priority(int $priority = -50)
    {
        $this->hook->priority = $priority;
        return $this;
    }
}
```

At this point you can create your listener for the new event and register it as per the Laravel documentation.