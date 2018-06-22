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
- Users need to be able to log in on the landing page so we can attach
a subscription plan to them (not part of this tutorial).
- New users choose a vanity name for their tenant portal which is created
immediately after sign up.

## tenant/system paradigm

Every time you create or use a model you have to identify where this
model lives. As the user should be able to log in on our portal, that model
will need to use the system connection. The landing page itself will not
show much other information, so most (if not all) other logic is part
of the tenant portal. For that reason it might be smart to set the
default connection to the tenant.

> Forcing the default connection to the tenant will make all Eloquent models
use this connection. Including those of third parties. 

We can force this using these methods:

- `DB_CONNECTION=tenant` in your `.env`.
- `TENANCY_DEFAULT_CONNECTION=tenant` in your `.env`.
- modify the configuration  in `tenancy.php` directly under `db > default`. 

Let's go for the first option for now.

Next up is making `App\User` read from the system connection. In order to do this
natively we:

- Apply the [UsesSystemConnection trait][4]:
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

[1]: requirements
[2]: installation
[3]: concepts
[4]: models#traits
