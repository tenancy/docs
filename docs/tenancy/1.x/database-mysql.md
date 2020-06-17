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

- MySQL server with a version >= `5.7.6`
- When using the [`hooks-database`](hooks-database) package the MySQL user defined in `database.php` must have the ability to create users, databases, and grant privileges.
- This package also required the installation and configuration of the [`affects-connections`](https://tenancy.dev/docs/tenancy/1.x/affects-connections) package. 
  (*Note:* This package is not required for the use of the [`affects-connections`](affects-connections) package)

**Recommendations**

- To automate the management of Tenant Databases when a Tenant is created, updated, or deleted; The [`hooks-database`](hooks-database) package should also be installed.

 **Use Cases**

- A Tenant's information needs to be stored in a separate database from other Tenants.
- and Many More!

## Requirements

- Because this package uses the MySQL command `IF NOT EXISTS` is used which is only supported in versions >= `5.7.6`

- Because this package runs specific "elevated permissions" queries in order to provide a database and a database user, the user defined in `database.php` needs to have the ability to create users, databases, and grant privileges.

  The following is a sample query to create a user with those privileges. 

```mysql
CREATE DATABASE IF NOT EXISTS tenancy;
CREATE USER IF NOT EXISTS tenancy@localhost IDENTIFIED BY 'someRandomPassword';
GRANT ALL PRIVILEGES ON *.* TO tenancy@localhost WITH GRANT OPTION;
```

> **Note:** The above command is simply an example. It will grant full access to all databases to the `tenancy` user. Please consult your teams security professional.

- The  `affects-connections` package is required by this package in order to transfer the database from one database user to another when the database user. This change occurs when the Tenant is updated causing a new database username within the `hooks-database` package.

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

