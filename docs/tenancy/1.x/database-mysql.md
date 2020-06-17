---
title: Database driver MySQL
icon: fal fa-warehouse-alt
excerpt: >
    Maintains the tenant MySQL database.
tags:
    - database
    - tenant
    - driver
    - mysql
---
# Database Driver: MySQL

1. [Overview](#overview)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [Configuration](#configuration)

## Overview

**Purpose**

The purpose of this package is to enable the management (Creation, Updating, Deletion) of Tenant MySQL Databases and MySQL Users.

**Requirements**

When using the [`hooks-database`](hooks-database) package the MySQL user defined in `database.php` must have the ability to create users, databases, and grant privileges.

**Recommendations**

- To automate the management of Tenant Databases when a Tenant is created, updated, or deleted; The [`hooks-database`](hooks-database) package should also be installed.
- To use the Tenant Database with the `onTenant` trait; The [`affects-connections`](https://tenancy.dev/docs/tenancy/1.x/affects-connections) package should also be installed. (*Note:* This package is not required for the use of the [`affects-connections`](affects-connections) package)

 **Use Cases**

- A Tenant's information needs to be stored in a separate database from other Tenants.
- and Many More!

## Requirements

Because this package runs specific "elevated permissions" queries in order to provide a database and a database user, the user defined in `database.php` needs to have the ability to create users, databases, and grant privileges.

The following is a sample query to create a user with those privileges. 

```mysql
TODO
```

## Installation

Install using composer:

```bash
composer require tenancy/db-driver-mysql
```

## Configuration

Detailed configuration steps are located in the [`hooks-database`](hooks-database) package and the [`affects-connections`](affects-connections) package.

In general there are several ways of setting the tenant database configuration for MySQL:

- Reusing a connection configured in `database.php` under `connections`.
- Load a configuration array from a separate file.

### Example

In the example below we will configure the database to be created using the information from the `mysql` database connection defined in the `config/database.php` and add Tenancy's default database settings to this.

```php
$event->useConnection('mysql', $event->defaults($event->tenant));
```

