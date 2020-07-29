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
2. [Installation](#installation)
3. [Configuration](#configuration)
    1. [Example](#example)

## Overview

**Purpose**

The purpose of this package is to allow the logging of errors into a separate file, location, or service for each tenant.

**Use Cases**

- Resolve specific tenant issues without going though logs for every tenant
- Find common problems across multiple tenants
- Log errors in different locations

**Events & Methods**

- `Tenancy\Affects\Logs\Events\ConfigureLogs`

## Installation

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/affects-logs
```

### Using Tenancy/Tenancy or with provider discovery disabled
Register the following ServiceProvider: 
  - `Tenancy\Affects\Logs\Provider::class`

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
