---
title: URLs
icon: fal fa-link
excerpt: >
    How you can easily affect the urls in your Laravel application.
tags:
    - affects
    - tenant
    - url
---

# Affects-URLs

1. [Overview](#overview)
2. [Installation](#installation)
3. [Configuration](#configuration)
    1. [Example](#example)

## Overview

**Purpose**

The purpose of this package is to modify domain being used when generating URL's.

**Use Cases**

- Tenants access the application using a custom domain
- Tenants receive custom subdomains to access the application

**Events & Methods**

- `Tenancy\Affects\URLs\Events\ConfigureURL`
  - `changeRoot`

## Installation

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/affects-urls
```

### Using Tenancy/Tenancy or with provider discovery disabled
Register the following ServiceProvider: 
  - `Tenancy\Affects\URLs\Provider::class`

## Configuration
Once you've installed the package, there's only some small configuring to do. You can do this by listening to the `Tenancy\Affects\URLs\Events\ConfigureURL` event. This event provides you with some additional classes and functionality you can use.
- `changeRoot()`, allows you to change the root url of the application.

### Example
In the below example, we'll change the URL to the tenant's name suffixed with `.test`.
```php
namespace App\Listeners;

use Tenancy\Affects\URLs\Events\ConfigureURL;

class ConfigureApplicationUrl
{
    public function handle(ConfigureURL $event)
    {
        if($tenant = $event->event->tenant)
        {
            $event->changeRoot($tenant->name . '.test');
        }
    }
}
```
