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

# Affects-Filesystems

1. [Overview](#overview)
2. [Installation](#installation)
3. [Configuration](#configuration)
    1. [Example](#example)

## Overview

**Purpose**

The purpose of this package is to allow the separation of tenant files, directories or even storage servers.

**Use Cases**

- Store Tenant files in different S3 Buckets
- Store Tenant files using different storage providers

**Events & Methods**

- `Tenancy\Affects\Filesystems\Events\ConfigureDisk`


## Installation

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/affects-filesystems
```

### Using Tenancy/Tenancy or with provider discovery disabled
Register the following ServiceProvider: 
  - `Tenancy\Affects\Filesystems\Provider::class`

## Configuration
When you're configuring this package, you will have to listen to the `Tenancy\Affects\Filesystems\Events\ConfigureDisk` event. This event will allow you to change the configuration for a `tenant` disk, simply by changing the config that is provided.

### Example
In this example we will register the `tenant` disk driver as a local driver with a folder in the `storage/app/` folder.
```php
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
