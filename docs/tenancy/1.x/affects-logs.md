---
title: Logs
icon: fal fa-list
excerpt: >
    Makes the logs adapt to your tenant identification.
tags:
    - affects
    - tenant
    - logs
---

## Introduction
Debugging your application is really important. Tenant aware logs are very handy when it comes to debugging your application. You can look at every single tenant individually and search for problems common across tenants really easily.

## Installation
Install via composer:
```bash
composer require tenancy/affects-logs
```

## Configuring
Configuration of this package is fairly easy, you can listen to the `Tenancy\Affects\Logs\Events\ConfigureLogs` event and provide a log configuration for your tenant driver.

## Example
In the below example we will configure a `tenant` log driver which will log the errors to a slack channel configure on the tenant.
```php
<?php

namespace App\Listeners;

use Tenancy\Affects\Logs\Events\ConfigureLogs;

class ConfigureTenantLogs
{
    public function handle(ConfigureLogs $event)
    {
        $event->config = [
            'driver' => 'slack',
            'url' => $event->event->tenant->slack_webhook_url,
            'username' => 'Tenant Logs',
            'emoji' => ':boom:',
            'level' => 'critical',
        ];
    }
}
```