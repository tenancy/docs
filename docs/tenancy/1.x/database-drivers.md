---
title: Database drivers
icon: fal fa-warehouse-alt
excerpt: >
    The database drivers able to service the data required by your application.
tags:
    - database
    - tenant
    - driver
---
# Database Drivers

A Database Driver is responsible for the actual management (Creation, Updating, Deletion) of the Tenant's database and/or database user. Most of the Database Drivers will run specific "elevated permissions" queries in order to provide a database and/or a database user.

The principles of the Database Drivers are quite simple. The `DatabaseResolver` will fire a simple set of events that allow the database drivers to hook into. They will provide a class that implements `ProvidesDatabase`.

Yes, you can install multiple database drivers, thus allowing you to use a different connection per [tenant](what-is-a-tenant) based on your requirements.

## Available Drivers

There are some Database Drivers that are maintained by the Tenancy organization, some might also be maintained by our Community.

Each Database Driver will have their own installation guide.

### First Party Drivers

- [MySQL](database-mysql)
- [SQLite](database-sqlite)
