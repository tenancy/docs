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

## Introduction

`Hooks-hostname` is all about working with hostnames of a tenant. A tenant might have one or multiple domains it is served over. During the lifecycle of this tenant it might change a few times and you need to take care of some really specific things.

This hooks allows you to register so called `HostnameHandlers`. These handlers will take care of anything related to the hostname of a tenant. There might be several different things you want to do:
- Register a new SSL Certificate
- Register the domain
- Change some configuration files
- Test the DNS to make sure the records are configured correctly.

## Installation
Installation via Composer:
```
composer require tenancy/hooks-hostname
```

## Configuring
Like most other hooks, this hook fires a simple event that allows you to configure it properly. The event is `\Tenancy\Hooks\Hostname\Events\ConfigureHostnames` and it has some really simply functionality:
- `registerHandler()`, which allows you to register a handler.
- `getHandlers()`, which allows you to get the handler.


## Example
In this simple example we try to check if the domains of the tenant are valid. If the tenant does not have valid domains, we will send an email to the administrator regarding the domains not being valid.

`app/Handlers/SimpleHandler.php`
```php
<?php

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

`app/Listeners/ConfigureHostnameHandlers.php`
```php
<?php

namespace App\Listeners;

class ConfigureHostnameHandlers
{
    public function handle(ConfigureHostnames $event)
    {
        $event->registerHandler(new SimpleHandler)
    }
}
```
