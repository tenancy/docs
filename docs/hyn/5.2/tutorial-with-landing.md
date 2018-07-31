---
title: Multi tenant with landing page
icon: fal fa-dolly-flatbed-alt
---
This tutorial will give you a brief introduction how to set up
a multi tenant app where your users will visit your landing page
to read about the product, sign up and log in. Once logged in
they are taken to their own tenant portal.

For you to jump in, make sure you followed the [requirements][1] and
[installation][2] articles. In addition I highly recommend reading
the [concepts][3] to understand the terminology.

Now let's specify what we need:

- Without a tenant website identified, we will show the landing page.
- Users need to be able to sign up on the landing page so they can configure
the details of their instance, eg their subscription plan (not part of this
tutorial).
- New users choose a vanity name for their tenant portal which is created
immediately after sign up.

## tenant/system paradigm

Every time you create or use a model you have to identify where this
model lives. As the user should be able to log in on our portal, that model
will need to use the system connection. The landing page itself will not
show much other information, so most (if not all) other logic is part
of the tenant. For that reason it might be smart to set the
default connection to the tenant.

> Forcing the default connection to the tenant will make all Eloquent models
use this connection. Including those of third parties. 

We can force this using any of these methods:

- `DB_CONNECTION=tenant` in your `.env`.
- `TENANCY_DEFAULT_CONNECTION=tenant` in your `.env`.
- modify the configuration  in `tenancy.php` directly under `db > default`. 

Let's go for the first option for now.

> Forcing the default connection to tenant can cause "connection 'tenant' not found"
errors whenever no tenant was identified. Keep this in mind.

Next up is making `App\User` read and write from the system connection. In order to do this
natively we:

- Apply the [UsesSystemConnection trait][4] to the User:
```php
namespace App;

use Hyn\Tenancy\Traits\UsesSystemConnection;

class User extends \Illuminate\Foundation\Auth\User
{
    use UsesSystemConnection;
}
```
- Update `config/auth.php`, update `passwords > users` so password resets
are created on the system database:
```php

    'passwords' => [
        'users' => [
            'provider' => 'users',
            'email' => 'spark::auth.emails.password',
            'table' => 'password_resets',
            'expire' => 60,
            'connection' => 'system'
        ],
    ],
```

This concludes initial set up. We have now forced all Eloquent models onto
the tenant connection, except for the User model. We need this to authorize 
visitors on our landing page and redirect them to their own portal.

## user - tenant

So the tenant is a Website model, but we're authorizing using a User. As such we
require a relation from the User to the Website:

 ```php
 namespace App;
 
 use Hyn\Tenancy\Models\Website;
 use Hyn\Tenancy\Traits\UsesSystemConnection;
 
 class User extends \Illuminate\Foundation\Auth\User
 {
     use UsesSystemConnection;
     
     public function website() 
     {
        return $this->belongsTo(Website::class);
     }
 }
 ```
 
 In addition we need to update the user migration, shipped per default
 but we need to add another column for the new relation called `website_id`:
 
 ```php
 use Illuminate\Support\Facades\Schema;
 use Illuminate\Database\Schema\Blueprint;
 use Illuminate\Database\Migrations\Migration;
 
 class CreateUsersTable extends Migration
 {
     /**
      * Run the migrations.
      *
      * @return void
      */
     public function up()
     {
         Schema::create('users', function (Blueprint $table) {
             $table->increments('id');
             $table->string('name');
             $table->string('email')->unique();
             $table->string('password');
             // Let's add the integer website_id:
             $table->integer('website_id')->nullable();
             $table->rememberToken();
             $table->timestamps();
         });
     }
     /**
      * Reverse the migrations.
      *
      * @return void
      */
     public function down()
     {
         Schema::dropIfExists('users');
     }
 }
 ```

## landing page

We need to create the landing page `resources/views/landing.blade.php` so we can
add the sign up form for new users to create their tenant environment.

```blade
<!doctype html>
<html lang="{{ app()->getLocale() }}">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">

        <title>Laravel</title>

        <!-- Fonts -->
        <link href="https://fonts.googleapis.com/css?family=Raleway:100,600" rel="stylesheet" type="text/css">

        <!-- Styles -->
        <style>
            html, body {
                background-color: #fff;
                color: #636b6f;
                font-family: 'Raleway', sans-serif;
                font-weight: 100;
                height: 100vh;
                margin: 0;
            }

            .full-height {
                height: 100vh;
            }

            .flex-center {
                align-items: center;
                display: flex;
                justify-content: center;
            }

            .position-ref {
                position: relative;
            }

            .content {
                text-align: center;
            }

            .title {
                font-size: 84px;
            }

            .links > a {
                color: #636b6f;
                padding: 0 25px;
                font-size: 12px;
                font-weight: 600;
                letter-spacing: .1rem;
                text-decoration: none;
                text-transform: uppercase;
            }

            .m-b-md {
                margin-bottom: 30px;
            }
        </style>
    </head>
    <body>
        <div class="flex-center position-ref full-height">
            <div class="content">
                <div class="title m-b-md">
                    <form method="post">
                        <input type="string" name="vanity">
                        <input type="submit" name="create">
                    </form>
                </div>

                <div class="links">
                    <!-- some links here -->
                </div>
            </div>
        </div>
    </body>
</html>
```

In order for the view to become part of our app, we need an additional
controller and route defined.

`app\Http\Controllers\LandingController.php`:

```php
namespace App\Http\Controllers;

use App\Http\Controllers\Controller;

class LandingController extends Controller
{
    public function __invoke()
    {
        return view('landing');
    }
}
```

> The `__invoke` is a magic method. If you assign a class name as route action
(like we did above) the method `__invoke` will be
run automatically. These are called [single action controllers][6]. All arguments
defined in the method will be automatically resolved from the [Service Container][8].

Now let's add a route entry to our web routes. We'll name the route "landing"
so we can use it within our application (other template files for instance).

`routes/web.php`:
```php
// .. other routes
Route::get('landing', 'LandingController')->name('landing');
```

When we now visit our app, eg `http://tenancy.test/landing`, we'll see 
the landing blade with the form asking for a vanity name.

But because we want to connect the tenant portal to the user, we need
to demand a user logs in before showing the route, update the route to match:

`routes/web.php`:
```php
// .. other routes
Route::get('landing', 'LandingController')->name('landing')->middleware('auth');
```

You can read more about [protecting routes in the Laravel docs][9].

## tenant home

We're going to rely on the ability to fully replace the global routes
with those of the tenant. The [tenant route override][5] requires us to
create a `routes/tenants.php` file:

```php
use App\Tenant\Controllers\HomeController;

Route::get('/', HomeController::class)->name('tenant.home');
```

> Now that we created this file, the package will automatically load these when
a tenant was identified. In case the same routes exist in the global scope 
(web.php or api.php), they will be overloaded.

We're referencing a controller, let's also create that one at 
`app/Tenant/Controllers/HomeController.php`:

```php
namespace App\Tenant\Controllers;

use App\Http\Controllers\Controller;

class HomeController extends Controller
{
    public function __invoke(Tenant $tenant)
    {
        return view('tenant.home', compact('tenant'));
    }
}
```

Next, is the view `tenant.home`. We want to have an identical view for all our
tenants, so this view file will live inside `resources/views`.

> In case you would have wanted to use views specific to a tenant, take a look
the ability to [add or replace views per tenant][7].

Create the file `resources/views/tenant/home.blade.php` with the following contents:

```blade
<!doctype html>
<html lang="{{ app()->getLocale() }}">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">

        <title>Laravel</title>

        <!-- Fonts -->
        <link href="https://fonts.googleapis.com/css?family=Raleway:100,600" rel="stylesheet" type="text/css">

        <!-- Styles -->
        <style>
            html, body {
                background-color: #fff;
                color: #636b6f;
                font-family: 'Raleway', sans-serif;
                font-weight: 100;
                height: 100vh;
                margin: 0;
            }

            .full-height {
                height: 100vh;
            }

            .flex-center {
                align-items: center;
                display: flex;
                justify-content: center;
            }

            .position-ref {
                position: relative;
            }

            .content {
                text-align: center;
            }

            .title {
                font-size: 84px;
            }

            .m-b-md {
                margin-bottom: 30px;
            }
        </style>
    </head>
    <body>
        <div class="flex-center position-ref full-height">
            <div class="content">
                <div class="title m-b-md">
                    This is: {{ $tenant->uuid }}
                </div>
            </div>
        </div>
    </body>
</html>
```

## create a tenant




[1]: requirements
[2]: installation
[3]: concepts
[4]: models#traits
[5]: fallback#tenant-routes-override
[6]: https://laravel.com/docs/5.6/controllers#single-action-controllers
[7]: structure
[8]: https://laravel.com/docs/5.6/container
[9]: https://laravel.com/docs/5.6/authentication#protecting-routes
