---
title: FAQ - Frequently Asked Questions
icon: fal fa-question
excerpt: >
    Frequently Asked Questions, common problems and some possibly not immediately obvious information
tags:
    - tenant
    - faq
---

# FAQ - Frequently Asked Questions

## Migrations
**Problem**

I've set up the lifecycle-hooks Database and Migration. When creating a new tenant all my migrations for tenant databases are run.
No matter what I try (`artisan migrate`, `artisan migrate --tenant`, ...), I can't get new migrations to be applied on existing tenant databases.


**Solution**

Migrations for tenant databases are triggered by [lifecycle events](hooks-general#events).
So, all you have to do is to fire an `Updated` event for the tenant you want to migrate. **Yes, this does mean you need to fire one event for each of your tenants if you want to migrate all at once.**

You can do this via `artisan tinker`, create a custom artisan command (e.g. `migrate:tenants`, inside your own tenant managing commands, ...), etc.

The code might look something like this:
```php
\App\Tenant::all()->map(function($tenant) { event(new Updated($tenant)); });
```


## SQL error while deleting a tenant
**Problem**

When deleting a tenant I receive a SQL error indicating it might have something to do with the deleted tenant

**Solution**

Migrations are run **after** database actions. So, in this case, Tenancy tries to run some migrations on the tenants database, by the tenants user, which at this point both won't exist anymore.

You might want to change your `ConfigureMigrations` listener to take that into account:
```php
class ConfigureTenantMigrations
{
    public function handle(ConfigureMigrations $event)
    {
        $event->path(database_path('tenant/migrations'));

        if($event->event instanceof Deleted) {
            $event->disable();
        }
    }
}
```

## Form validations are run against the system database instead of the tenants database
**Problem**

Laravel keeps trying to run validations against my system database, even though my models are using the `OnTenant` trait. What's going on?

*Example*

Let's take a default Laravel UI scaffold including auth. The *RegisterController* looks something like this:
```php
[... snip ...]
protected function validator(array $data)
{
    return Validator::make($data, [
        'name' => ['required', 'string', 'max:255'],
        'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
        'password' => ['required', 'string', 'min:8', 'confirmed'],
    ]);
}
[... snip ...]
```
You can clearly see the validation rule stating *unique:users*. So, what gives?

**Solution**

It's simple. Form validation in Laravel **does not** use your models! So, to get it to work, you need to tell Laravel that you want to validate against a table in a different database.

Luckily, all you need to do is to prefix the table name with the right connection name.
```php
        'email' => ['required', 'string', 'email', 'max:255', 'unique:tenant.users'],
```
There, that's all you need!
