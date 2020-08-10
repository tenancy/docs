---
title: Hooks Hostname
icon: fal fa-heartbeat
excerpt: >
    How tenancy works with the Tenant lifecycle for Hostname mutations.
tags:
    - tenant
    - hooks
    - hostname
---

# Lifecycle Hook: Hostname

- [Overview](#overview)
- [Introduction](#introduction)
- [Installation](#installation)
- [Configuration](#configuration)
  - [Tenant](#tenant)
  - [Handlers](#handlers)
  - [Hook](#hook)

## Overview

**Purpose**

The purpose of this package is to allow running custom functions when a Tenant is updated.

**Requirements**
- A [Tenant](what-is-a-tenant) that dispatches the `Created`, `Updated`, and/or `Deleted` [lifecycle events](hooks-general#events).

**Use Cases**

- Register a new SSL Certificate
- Register a domain
- Change some configuration files
- Test the DNS to make sure the records are configured correctly
- and Many More!

**Events & Methods**

- `\Tenancy\Hooks\Hostname\Events\ConfigureHostnames`
  - `disable()`
  - `priority()`
  - `registerHandler()` 
  - `getHandlers()`

## Introduction

`Hooks-hostname` is all about working with hostnames of a tenant. A tenant might have one or multiple domains it is served over. During the lifecycle of this tenant it might change a few times and you need to take care of some really specific things.

This hook allows you to register so-called `HostnameHandlers`. These handlers will take care of anything related to the hostname of a tenant. 

## Installation

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/hooks-hostname
```

### Using Tenancy/Tenancy or with provider discovery disabled
Register the following ServiceProvider: 
  - `Tenancy\Hooks\Hostname\Provider::class`

## Configuration
### Tenant

To begin, your Tenant's needs to be updated to use the `Tenancy\Hooks\Hostname\Contracts\HasHostnames` Contract and implement the `getHostnames` function returning an array of hostnames for the Tenant.

In the following example we will assume that the Tenant has a "hostname" attribute.

```php
class Tenant implements \Tenancy\Hooks\Hostname\Contracts\HasHostnames
{
    public function getHostnames()
    {
        return [
            $this->hostname
        ];
    }
}
```

### Handlers

Next, Handlers need to be created. They perform the logic to execute for each hostname such as registering a domain, updating nginx/apache configuration files, etc.

To create a handler, you need to create a new class that implements the `Tenancy\Hooks\Hostname\Contracts\HostnameHandler` contract and implements the `handle` function to perform the required logic.

In the following example we try to check if the domains of the tenant are valid. If the tenant does not have valid domains, we will send an email to the administrator regarding the domains not being valid.

```php
namespace App\Handlers;

use Tenancy\Hooks\Hostname\Contracts\HostnameHandler;
use Tenancy\Tenant\Events\Event;
use Illuminate\Support\Facades\Mail;

class SimpleHandler implements HostnameHandler
{
    public function handle(Event $event): void
    {
        if($this->hasValidDomains($event->tenant)){
        Mail::to($event->tenant->email)->send(new DomainsNotValid($event->tenant->getHostnames()));
        }
    }
}
```

### Hook

Like most other hooks, this hook fires a simple event that allows you to configure it properly. The event is `\Tenancy\Hooks\Hostname\Events\ConfigureHostnames` and it has some really simply functionality:

- `registerHandler()`, which allows you to register a handler.
- `getHandlers()`, which allows you to get the handler.

In the following example we will register the handler created in the previous example.

```php
namespace App\Listeners;

class ConfigureHostnameHandlers
{
    public function handle(ConfigureHostnames $event)
    {
        $event->registerHandler(new MyHandler)
    }
}
```