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
The principles of the Database Drivers are quite simple. The `DatabaseResolver` will fire a simple set of events that allow the database drivers to hook into. They will provide a class that implements `ProvidesDatabase`.

Yes, you can install multiple database drivers, thus allowing you to use a different
connection per [tenant](what-is-a-tenant) based on your requirements.

- [MySQL](database-mysql)
- [SQLite](database-sqlite)
