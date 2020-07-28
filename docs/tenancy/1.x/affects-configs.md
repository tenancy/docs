---
title: Configs
icon: fal fa-sliders-v
excerpt: >
    How you can easily affect the configs in your Laravel application.
tags:
    - affects
    - tenant
    - configs
---

# Affects-Config

1. [Overview](#overview)
2. [Installation](#installation)
3. [Configuration](#configuration)
    1. [Example](#example)

## Overview

**Purpose**

The purpose of this package is to allow the use change the configuration for different integrations or packages based on the current tenant.

**Use Cases**

- Use a tenant's API credentials for a 3rd party service

**Events & Methods**

- `Tenancy\Affects\Configs\Events\ConfigureConfig`

> All the calls you do to the event, will be forwarded to the `Repository` (the class that holds all the configs).

## Installation
Install via composer
```bash
composer require tenancy/affects-configs
```

## Configuration
After the installation, it's all about configuring. Like other affects, you can listen to an event `Tenancy\Affects\Configs\Events\ConfigureConfig`. When you're listening to the event, you can get to work right away.

### Example
In the below example, we'll take the integration key from the tenant and set it in our configuration so the integration can use it.
```php
namespace App\Listeners;

use Tenancy\Affects\Configs\Events\ConfigureConfig;

class ConfigureTenantIntegrations
{
    public function handle(ConfigureConfig $event)
    {
        if($tenant = $event->event->tenant)
        {
            $event->set('integrations.integration.key', $tenant->integration_key);
        }
    }
}
```
