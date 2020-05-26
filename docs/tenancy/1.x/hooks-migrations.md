---
title: Migrations and seeds
icon: fal fa-inventory
excerpt: >
    Automatically run migrations and seeds on tenant
    lifecycle events.
tags:
    - hooks
    - tenant
    - migrations
    - seeds
---

## Installation
`Hooks-migration` is a package that is a bit more complex than most Tenancy packages. This will take care of everything after a Database is created for your multi database setup.


### Hooks Migration
```bash
composer require tenancy/hooks-migration
```

#### Configuring Migrations
Once you've installed the package, you will have to configure how Tenant should be migrated. You can do this by listening to the `\Tenancy\Hooks\Migration\Events\ConfigureMigrations` event. It will provide you with some simple functionality in order to make your Migrations work like a charm:
- `path()`, this function allows you to register a path where your migrations are located. These migrations will be used once the migrations run.
- `disable()`, this function allows you to completely disable the migrations.

#### Migrations Example
In the example below we'll add the `database/tenant/migrations` folder containing migrations that should be used for the Tenant database.

```php
<?php

namespace App\Listeners;

use Tenancy\Hooks\Migration\Events\ConfigureMigrations;

class ConfigureTenantMigrations
{
    public function handle(ConfigureMigrations $event)
    {
        $event->path(database_path('tenant/migrations'));
    }
}
```

#### Running migrations manually
If you have migration hook set up, you can fire `Tenancy\Tenant\Events\Updated` event, which will trigger hooks and run the migrations:
```php
event(new \Tenancy\Tenant\Events\Updated($tenant));
```

Alternativelly, you can use `php artisan migrate` command by passing addditional parameters:
* `--tenant` parameter will specify which tenant to run migrations for. This requires [console identification](identification-console).
* `--database` parameter will tell migrations to run on `tenant` connection, which is set up dinamically using [affects-connections](affects-connections). 
* `--path` parameter will specify migration path.

Example:
```
php artisan migrate --tenant=1 --database=tenant --path=database/tenant/migrations
```


#### Configuring Seeding
Once you've installed the migrations, you might want to seed the database with some data for the tenant. In that case you can listen to the `\Tenancy\Hooks\Migration\Events\ConfigureSeeds` event. This event has one very simple method that will be of use to you:
- `seed()`, this function will take the string of the seeder class. You can see how to use this in the example below.

```php
<?php

namespace App\Listeners;

use Tenancy\Hooks\Migration\Events\ConfigureSeeds;

class ConfigureTenantSeeds
{
    public function handle(ConfigureSeeds $event)
    {
        $event->seed(\UsersSeeder::class);
    }
}
```

### Affects Connections
Affects Connections is used in order to use the tenant specific connection for your migrations. We highly recommend you to look at the setup [here](affects-connections)
