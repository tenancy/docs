---
title: Connections
icon: fal fa-warehouse-alt
---

#### Database Connections

One of the core aspects of tenancy is the database connection. Tenancy 
gives you access to two key database connections; `tenant` and `system`.

#### System Connection
Out of the box, the package gives you a `system` database connection which acts as 
the master database where `hostnames`, `websites` and `customers` are stored.

If for some reason you need to create an Eloquent Model that would need to connect 
to the system connection, you can apply the `Hyn\Tenancy\Traits\UsesSystemConnection` 
trait on your given Model. In doing so, the model’s connection will always default to use the `system`.

One reason for using the `system` connection is when you have data which you'd like 
to share between all tenants or which you need to influence the tenant identification 
process. For instance when you are creating an admin portal that would manage all of your 
tenants in one place.

#### Tenant Connection
This is maybe the most used database connection. Data in use by single tenant instances, 
not shared with others, is persisted into the database using the tenant connection. The 
implementation of the tenant connection ensures that no tenant can access another tenants data.

By using the `Hyn\Tenancy\Traits\UsesTenantConnection` trait, the package will make sure 
that it is using the right database connection.

All tenant related models should use the `UsesTenantConnection` trait.
