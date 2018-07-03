---
title: Models
icon: fal fa-hand-holding-box
---

For you to more easily connect with the correct database a few ways are possible.

#### Traits

Two traits exist to force a model onto either the tenant or system connection.

- `Hyn\Tenancy\Traits\UsesSystemConnection` to make a model use the system connection.
- `Hyn\Tenancy\Traits\UsesTenantConnection` to make a model use the tenant connection.

The below snippet shows an example of how to force the User model to use the tenant
connection.

```php
namespace App;

use Hyn\Tenancy\Traits\UsesTenantConnection;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use Notifiable, UsesTenantConnection;
    
    // ..
}
```

#### Abstract models

Instead of using the traits one can also simply have the models extend the abstract
models provided in the package.

- `Hyn\Tenancy\Abstracts\SystemModel` can be extended to give easy access to 
the system database connection.
- `Hyn\Tenancy\Abstracts\TenantModel` can be extended to have your Eloquent models 
use the connection of the identified tenant per default.

> Please note these models are using their respective traits.

#### System model override

This package allows you to specify different models to be used for the 
Hostname and Website models. Tenancy will take care of the relationships on the 
package-end. You also need to implement `Hyn\Tenancy\Contracts\{Model}`

Take this example:

```php
<?php

namespace App;

use Hyn\Tenancy\Contracts\Website as Contract;

class Website implements Contract
{
}
```

All you need to do is update the `tenancy.php` configuration file:

```php
<?php
return [
  'models' => [
      // ..
      // Must implement \Hyn\Tenancy\Contracts\Hostname
      'hostname' => \Hyn\Tenancy\Models\Hostname::class,
      // Must implement \Hyn\Tenancy\Contracts\Website
      'website' => \App\Website::class
  ],
  // ...
];
```
