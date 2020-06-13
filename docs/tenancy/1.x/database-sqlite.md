---
title: Database driver SQLite
icon: fal fa-warehouse-alt
excerpt: >
    Maintains the tenant SQLite database.
tags:
    - tenant
    - database
    - driver
    - sqlite
---

# Installation
Install using composer:

```bash
composer require tenancy/db-driver-sqlite
```

This package will allow you to use the `hooks-database` for SQLite Database instances.

> CAUTION: This will only provide **local** databases, which are not remote accessibly.

> CAUTION: This database driver might require additional steps for configuration. It is based on a filesystem, and not a real database like most Database Drivers.
