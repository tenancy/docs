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
The principles of the database driver is quite similar to the [identification driver][identification-drivers];
the connection resolver dispatches a set of events which allow our database drivers to tell what connection to
set up for the tenants. Yes, you can install multiple database drivers, thus allowing you to use a different
connection per [tenant][what-is-a-tenant] based on your requirements.

- [MySQL](database-driver-mysql)
- [SQLite](database-driver-sqlite)

[identification-drivers]: identification-drivers
[what-is-a-tenant]: what-is-a-tenant
