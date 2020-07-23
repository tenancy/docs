---
title: Logs
icon: fal fa-book
excerpt: >
    Makes the logs adapt to your tenant identification.
tags:
    - affects
    - tenant
    - logs
---

# Affects-Logs

1. [Overview](#overview)
3. [Installation](#installation)
4. [Configuration](#configuration)
    1. [Example](#example)

## Overview

**Purpose**

The purpose of this package is to allow the logging of errors into a separate file or location for each tenant.

**Use Cases**

- Resolve specific tenant issues without going though logs for every tenant
- Find common problems across multiple tenants
- Log errors in different locations

**Events & Methods**

- `Tenancy\Affects\Logs\Events\ConfigureLogs`

## Installation
Install via composer:
```bash
composer require tenancy/affects-logs
```

## Configuration
Configuration of this package is fairly easy, you can listen to the `Tenancy\Affects\Logs\Events\ConfigureLogs` event and provide a log configuration for your tenant driver.

### Example
In the below example we will configure a `tenant` log driver which will log the errors to a slack channel configure on the tenant.
```php
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
