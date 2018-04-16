---
title: Migrations
icon: fal fa-pallet-alt
---

Migrations are extremely important in the Laravel ecosystem. To facilitate
tenants with this flow, without interfering with its logic migration commands
for tenants have been given their own namespace `tenancy:`.

# About the database/migrations path

It's not wise to store the tenants migrations inside your global
`database/migrations` directory. Laravel by default runs these migrations
when calling the `php artisan migrate` command. Even though you can suggest
a connection during that command, it's much cleaner to store these migrations
inside a separate directory, e.g. `database/migrations/tenant`. 
The migrations for the system database can stay in `database/migrations`.

# Default migrations for new tenants

If you want new tenants to be migrated by default, you can easily do so. In
your `tenancy.php` configuration set `db > tenant-migrations-path` to a valid
absolute path and all your new tenants will run these migrations per default.

```php
  // ..
  "tenant-migrations-path" => database_path('migrations/tenant'),
  // ..
```

# Default seeds for newly auto-migrated tenants

In case you would also like to seed the databases of your newly created
and migrated tenants, you can enable `db > tenant-seed-class` in your `tenancy.php`
configuration file. Change this setting to a fully namespaced class name
and the package will automatically run this seed after it has ran the
automatic migrations.

# Migration related commands

Obviously tenancy is also able to run migrations running through artisan.

Each of the native Laravel migrate and seed command has its tenancy
counterpart, for instance.

- `tenancy:migrate`
- `tenancy:db:seed`

Please check their separate signatures for more information of valid arguments
and options.

> Running tenancy:migrate without any path or realpath option and 
without having configured tenant-migration-path will migrate your 
tenants with the files inside the database/migrations directory. 

# Reconstructing tenant databases

As long as your system database is intact, there's a way to recreate all tenant databases. You can use the `tenancy:recreate` to recreate all tenant databases that do not exist and will run the migrations and seeds according to your configuration as necessary.

Though the structure of all of your tenant databases will be recreated, the data in it won't.
