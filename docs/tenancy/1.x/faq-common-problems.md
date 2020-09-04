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

1. [Migrations in a tenant database (multi-db)](#migrations)

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
