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

## Introduction
A lot of modern sites use WebSockets in order to provide real-time notifications, updates and messages. In order to have maximum scalability for your application, you might want to have tenant aware broadcasts.

## Installation
Install via composer:
```bash
composer require tenancy/affects-broadcasts
```

## Configuring
Once you've installed the package, all you have to do is configure the package. You can configure this package by listening to the `Tenancy\Affects\Broadcasts\Events\ConfigureBroadcasts` event. Once you're listening to the event, you can start configuring your `tenant` broadcast driver with the provided `$config` array.

## Example
In the below example, we will create a pusher driver for the tenant broadcast driver.

> We assume that you have the pusher information on the tenant model.
```php
namespace App\Listeners;

use Tenancy\Affects\Broadcasts\Events\ConfigureBroadcasts;

class ConfigureTenantBroadcast
{
    public function handle(ConfigureBroadcasts $event)
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
