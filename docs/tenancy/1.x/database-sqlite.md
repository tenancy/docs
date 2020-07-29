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

# Database Driver: SQLite

1. [Overview](#overview)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [Configuration](#configuration)

## Overview

**Purpose**

The purpose of this package is to enable the management (Creation, Updating, Deletion) of Tenant SQLite Databases.

**Requirements**

When using the [`hooks-database`](hooks-database) package the the location where the SQLite database will be stored needs to be writeable by the user running the code. (I.E. `www-data`)

**Recommendations**

- To automate the management of Tenant Databases when a Tenant is created, updated, or deleted; The [`hooks-database`](hooks-database) package should also be installed.
- To use the Tenant Database with the `onTenant` trait; The [`affects-connections`](https://tenancy.dev/docs/tenancy/1.x/affects-connections) package should also be installed. (*Note:* This package is not required for the use of the [`affects-connections`](affects-connections) package)

 **Use Cases**

- A Tenant's information needs to be stored in a separate database from other Tenants.
- A Tenants location needs to be stored in a location that is local to the webserver and not accessible outside of the filesystem it is stored on.
- and Many More!

## Requirements

Because this package will create a new SQLite database file, the location that database files are being created needs to be writeable by the user running the code. (I.E. `www-data`)

## Installation

> CAUTION: This will only provide **local** databases, which are not remote accessibly.

> CAUTION: This database driver might require additional steps for configuration. It is based on a filesystem, and not a real database like most Database Drivers.

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/db-driver-sqlite
```

### Using Tenancy/Tenancy or with provider discovery disabled
Add the provider to `config/app.php`.

```php
    'providers' => [
        // ...
        
        /*
        * Package Service Providers...
        */
        Tenancy\Database\Drivers\Sqlite\Provider::class,
        
        // ...
    ]
```

## Configuration

Detailed configuration steps are located in the [`hooks-database`](hooks-database) package and the [`affects-connections`](affects-connections) package.

### Example

In the following example we set the connection to use SQLite, and set the database path to be specific to the tenant based on the Tenant's Key.

```php
$event->useConnection('sqlite', [
	'database' => database_path('tenant/'.$event->tenant->getTenantKey().'.sqlite'),
]);
```

