---
title: Spatie permission
excerpt: A tutorial on setting up spatie/laravel-permission.
icon: fal fa-file-archive
tags:
    - spatie
    - permission
    - tutorial
---
1. Create a fresh install of Laravel.  If you have installed the Laravel Terminal Installer run: `$ laravel new app`
2. `$ cd app`
3. Install `hyn/multi-tenant` package by running:
```bash
composer require hyn/multi-tenant
```
4. Follow the instructions until "Deploy configuration" section.
5. If you are using MySQL DB, you need to add the entry `LIMIT_UUID_LENGTH_32=true` in your `.env` file.

## Install Spatie Permissions

To install the package follow the entire installation section
at [GitHub - spatie/laravel-permission: Associate users with roles and permissions](https://github.com/spatie/laravel-permission#installation).

## Integration

This section of the tutorial assumes that you have successfully created at least 2 hostnames
and successfully have accessed them via your local environment setup.

We need to extend the two eloquent models (`Permission`, `Role`) of the Permissions
package so that we can add the necessary changes we need in order for these two models
to be tenant aware.

1. Create a `Permission.php` and `Role.php` file and place them inside your `app` directory,
next to your `User.php`. Make sure that these files extend `Spatie\Permission\Models\Permission::class`
and `Spatie\Permission\Models\Role::class` respectively.

`App\Permission.php` - extends from `SpatiePermission` and uses the `UsesTenantConnection` trait.
```php
use Hyn\Tenancy\Traits\UsesTenantConnection;
use Spatie\Permission\Models\Permission as SpatiePermission;

class Permission extends SpatiePermission
{
    use UsesTenantConnection;
}
```

`App\Role.php` - extends from `SpatieRole` and uses the `UsesTenantConnection` trait.
```php
use Hyn\Tenancy\Traits\UsesTenantConnection;
use Spatie\Permission\Models\Role as SpatieRole;

class Role extends SpatieRole
{
    use UsesTenantConnection;
}
```

`App\User.php` - notice the `HasRoles`, `UsesTenantConnection` traits we added.
```php
use Spatie\Permission\Traits\HasRoles;
use Illuminate\Notifications\Notifiable;
use Hyn\Tenancy\Traits\UsesTenantConnection;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use Notifiable, UsesTenantConnection, HasRoles;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name', 'email', 'password',
    ];

    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = [
        'password', 'remember_token',
    ];
}
```

3. Modify your `config/permission.php` file and modify the permission and role models
that is being used here to point to your newly created models. e.g. `App\Permission::class`

`config\permisssion.php`
```php
return [

    'models' => [

        'permission' => App\Permission::class,

        'role' => App\Role::class,

    ],

    ...
];
```

4. Creating a user in your first hostname.

```php
use App\User;
use App\Role;
use App\Permission;
use Hyn\Tenancy\Models\Hostname;

// Switch the environment to use first hostname
$environment = app(\Hyn\Tenancy\Environment::class);
$hostname = Hostname::find(1);
$environment->hostname($hostname);

// creates role and permission on your first hostname
$role = Role::create(['name' => 'writer']);
$permission = Permission::create(['name' => 'edit articles']);

// Retrieve user and check to make sure it has no roles yet
$user = User::find(1);
$user->getRoleNames();

// Assign writer role and save the $user instance to DB.
$user->assignRole('writer');
$user->save();

// Requery and check if the newly assigned roles exists.
$user = User::find(1);
$user->getRoleNames();
```
