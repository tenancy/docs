---
title: Broadcasts
icon: fal fa-wifi
excerpt: >
    Makes the broadcasts adapt to your tenant identification.
tags:
    - affects
    - tenant
    - broadcasts
---

# Affects-Broadcasts

1. [Overview](#overview)
2. [Installation](#installation)
3. [Configuration](#configuration)
    1. [Example](#example)

## Overview

**Purpose**

The purpose of this package is to allow the use of different broadcast drivers and settings based on the tenant.

**Use Cases**

- Use a tenant chosen broadcast driver.
- Use different Redis servers per tenant for broadcasts.

**Events & Methods**

- `Tenancy\Affects\Broadcasts\Events\ConfigureBroadcast`

## Installation

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/affects-broadcasts
```

### Using Tenancy/Tenancy or with provider discovery disabled
Register the following ServiceProvider: 
  - `Tenancy\Affects\Broadcasts\Provider::class`

## Configuration
Once you've installed the package, all you have to do is configure the package. You can configure this package by listening to the `Tenancy\Affects\Broadcasts\Events\ConfigureBroadcast` event. Once you're listening to the event, you can start configuring your `tenant` broadcast driver with the provided `$config` array.

### Example
In the below example, we will create a pusher driver for the tenant broadcast driver.

> We assume that you have the pusher information on the tenant model.
```php
namespace App\Listeners;

use Tenancy\Affects\Broadcasts\Events\ConfigureBroadcast;

class ConfigureTenantBroadcast
{
    public function handle(ConfigureBroadcast $event)
    {
        $event->config = [
            'driver' => 'pusher',
            'key' => $event->event->tenant->pusher_key,
            'secret' => $event->event->tenant->pusher_secret,
            'app_id' => $event->event->tenant->pusher_app_id,
            'options' => [
                'cluster' => $event->event->tenant->pusher_cluster,
                'useTLS' => true,
            ],
        ];
    }
}
```
