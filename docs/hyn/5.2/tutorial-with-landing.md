---
title: Multi tenant with landing page
icon: dolly-flatbed-alt
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
- Users need to be able to log in on the landing page so they can configure
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

We can force this using these methods:

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

## The tenant

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



[1]: requirements
[2]: installation
[3]: concepts
[4]: models#traits
