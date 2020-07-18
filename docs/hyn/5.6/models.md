---
title: Models
icon: fal fa-hand-holding-box
---

Tenancy offers a few ways to more easily connect with the correct database.

#### Traits

Two traits exist to force a model onto either the `tenant` or `system` connection.

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

Instead of using the traits directly one can also simply have the models extend the abstract
models provided in the package.

- `Hyn\Tenancy\Abstracts\SystemModel` can be extended to give easy access to 
the system database connection.
- `Hyn\Tenancy\Abstracts\TenantModel` can be extended to have your Eloquent models 
use the connection of the identified tenant per default.

> Please note these abstract models are using the respective traits mentioned above.

#### Relationships

Model eloquent relations are possible from tenant to system and vice versa. One thing to remember
with intermediate tables involved, for instance on the belongsToMany relationship, is that you can
prefix the intermediate table with the connection. 
 
For instance, when you have your User model on the system database - acting as a global user - you can
store the user roles on the tenant database by prefixing that intermediate table with the tenant database
name and assigning the Role model to the tenant connection. 

```php
    public function roles() {
        $config = app(\Hyn\Tenancy\Database\Connection::class)->configuration();
        return $this->belongsToMany('App\Role', "{$config['database']}.role_user");
    }
```

> This only works if tenant databases are on the same server as the system database! You cannot
use the managing system connection feature in this scenario.
