---
title: Filesystems
icon: fal fa-hdd
excerpt: >
    Makes the filesystems adapt to your tenant identification.
tags:
    - affects
    - tenant
    - filesystems
---

## Introduction
In order to keep tenant's data completely separate, you can decide to work with tenant aware filesystems.

## Installation
Install via composer
```bash
composer require tenancy/affects-filesystems
```

## Configuring
When you're configuring this package, you will have to listen to the `Tenancy\Affects\Filesystems\Events\ConfigureDisk` event. This event will allow you to change the configuration for a `tenant` disk, simply by changing the config that is provided.

## Example
In this example we will register the `tenant` disk driver as a local driver with a folder in the `storage/app/` folder.
```php
<?php

namespace App\Listeners;

use Tenancy\Affects\Filesystems\Events\ConfigureDisk;

class ConfigureTenantDisk
{
    public function handle(ConfigureDisk $event)
    {
        $event->config = [
            'driver' => 'local',
            'root' => storage_path('app/' . $event->event->tenant->getTenantKey()),
        ];
    }
}
```
