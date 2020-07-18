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

## Introduction
Some applications will require you to use integrations that are separated for each tenant. You will probably have to change the configurations that are used in the application on the fly to make sure you're using your tenant's integration and not your own.

## Installation
Install via composer
```bash
composer require tenancy/affects-configs
```

### Configuring
After the installation, it's all about configuring. Like other affects, you can listen to an event `Tenancy\Affects\Configs\Events\ConfigureConfig`. When you're listening to the event, you can get to work right away.
All the calls you do to the event, will be forwarded to the `Repository` (the class that holds all the configs).

## Example
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
