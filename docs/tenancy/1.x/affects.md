---
title: About affects
icon: fal fa-reply-all
excerpt: >
    How tenancy modifies your Laravel app.
tags:
    - affects
    - tenant
---

Affects change the behaviour of your Laravel application by integrating closely with the
framework. You can modify routes, views and much more by using an affect or creating
your own!

Affects are an important component to the workings of tenancy. Each affect influences your
Laravel app, but enabling them isn't enough for most of them. Affects fire events, which allow
you to hook into. This makes it incredibly easy to implement your own specific modifications
without the hard work of hooking into the Laravel ecosystem or knowing the inner workings of 
the tenancy framework.

The easiest way to listen to the events and influence the affects to do your bidding, is by
creating a listener in the EventServiceProvider. Let's take the [Routes Affects](affects-routes)
as an example.

First, create a listener.

```php
<?php

namespace App\Listeners;

use Tenancy\Affects\Routes\Events\ConfigureRoutes;

class TenantRoutes 
{
    public function handle(ConfigureRoutes $event) 
    {
        $event
            ->flush()
            ->fromFile(
                ['middleware' => ['web']],
                base_path('/routes/tenant.php')
            );
    }
}
```

Now register that listener in your EventServiceProvider for instance.

```php
<?php

namespace App\Providers;

use App\Listeners\TenantRoutes;
use Illuminate\Auth\Events\Registered;
use Illuminate\Auth\Listeners\SendEmailVerificationNotification;
use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;
use Tenancy\Affects\Routes\Events\ConfigureRoutes;

class EventServiceProvider extends ServiceProvider
{
    protected $listen = [
        Registered::class => [
            SendEmailVerificationNotification::class,
        ],
        
        ConfigureRoutes::class => [
            TenantRoutes::class,
        ]
    ];
    // ..
}

```

That's it, from now on whenever a tenant is identified or switched to, the routes
contained in `routes/tenant.php` are activated.
