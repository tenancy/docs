---
title: Non-Tenant Site Setup
icon: fal fa-gears
excerpt: >
    How to setup goabal and admin sites in the same application
tags:
    - tenancy
    - tutorial
    - non-tenant
---

## Introduction
When building a SaaS there tends to be a need for a "sub-site" such as an admin panel, or a public facing website describing your SaaS.

This tutorial will go though the basics of setting these "sub-sites" within the same codebase without the need for creating a tenant do display these sites.

## Requirements
This tutorial will assume that you have followed the [basic setup tutorial](tutorial-basic-setup).

## Routes
The first thing we want to do is configure the [Routes Affect](affacts-routes).

First start by creating a new file for all tenant specific routes.

### Tenant Route File
For this example it will be stored at `routes\tenant.php`
```php
use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| Tenant Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your tenant application.
| They are not loaded by default, and will be loaded thought the Routes Affect.
|
*/

Route::get('/', function () {
    //This is the "root" route for all tenants
});

```

### Routes Affect Listener
Next we need to create the listener for the [Routes Affect]() that will get rid of the system (non-tenant) routes and load our tenant route file instead.

`app\Listeners\ConfigureTenantRoutes.php`
```php
namespace App\Listeners;

use Tenancy\Affects\Routes\Events\ConfigureRoutes;

class TenantRoutes 
{
    public function handle(ConfigureRoutes $event) 
    {
        if($event->event->tenant)
        {
            $event
                ->flush()
                ->fromFile(
                    ['middleware' => ['web']],
                    base_path('/routes/tenant.php')
                );
        }
    }
}
```

#### Registering the listener
Now update your `app\Providers\EventServiceProvider.php` to include your new listener.

```php
  protected $listen = [

    ...

    Tenancy\Affects\Routes\Events\ConfigureRoutes::class => [
      App\Listeners\ConfigureTenantRoutes::class,
    ]
    
    ...
```

## Status

At this point you application is configured to load any tenant routes when a tenant is identified. When a tenant is not identified, the routes located in `routes/web.php` will be used instead.

## Tips and Reminders
### Namespaceing
As with any Laravel application; if you are storing controllers in a different location than in `app\Http\Controllers` you will need to use the namespace option within your routes.

`routes\tenant.php`
```php
Route::namespace('Tenant')->group(function () {
    // Controllers Within The "App\Http\Controllers\Tenant" Namespace
});
```

### Sub-site by domains
When using subdomains to identify your non-tenant sub-sites you will need to use the route option within your routes file.
`routes\web.php`
```php
Route::domain('admin.myapp.com')->group(function () {
    Route::get('/', function () {
        // This is the root route for the admin site
    });
});

Route::domain('www.myapp.com')->group(function () {
    Route::get('/', function () {
        // This is the root route for the public site
    });
});

Route::get('/shared', function() {
  //This is a route that is shared with both the admin site and the public site
})
```


## Considerations to take into account
1. **Storing users in tenant databases**
When the User Model has the `OnTenant` trait applied to it, you will not be able to have those users login to any "non-tenant" routes. Because there is no tenant identified, it will not be possible to authenticate the user.
To overcome this you will need to either create two different User models (Admin & User), or will need to store all users in the system database. The option you choose will depend on youre goals and objectives.

2. **Models using the `OnTenant` trait**
Much like the previous consideration, you will not be able to access any models stored within a tenant database without first identifying the tenant.
(Tutorial at a future date)

3. **Subdomain identification**
When using subdomain identification, it is best practice to preform an additional check when registering a new tenant that they do not try to use a subdomain you have configured for a "non-tenant site".
I.E. Do not allow users to register the subdomains (`www`, `admin`, etc...)