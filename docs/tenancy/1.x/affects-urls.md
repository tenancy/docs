---
title: URLs
icon: fal fa-reply-all
excerpt: >
    How you can easily affect the urls in your Laravel application.
tags:
    - affects
    - tenant
    - url
---

## Introduction
Once a tenant is identified, you might want to provide them with a really custom feel. Allowing tenants to affect the URL's that are used by the application can be an easy way to do this.


## Installation
Install using composer
```bash
composer require tenancy/affects-urls
```


### Configuring
Once you've installed the package, there's only some small configuring to do. You can do this by listening to the `Tenancy\Affects\URLs\Events\ConfigureURL` event. This event provides you with some additional classes and functionality you can use.
- `changeRoot()`, allows you to change the root url of the application.

## Example
In the below example, we'll change the URL to the tenant's name suffixed with `.test`.
```php
<?php

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
