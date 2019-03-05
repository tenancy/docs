---
title: Upgrading from 5.3
icon: fal fa-level-up-alt
---
The 5.4 version adds compatibility with Laravel 5.8 while fixing minor issues and adding
more features.

## Backward breaking changes

- Minimum PHP requirement now 7.2.
- The tenant database password is now generated differently. If `tenancy.key` is `null` it will use
the password generation seen in previous versions by generating an md5 has of the app.key and the website Id.
However if you set it to the recommended `env('TENANCY_KEY)` or a string of any kind, the password is now generated
with this value, the website Id, Uuid and creation date. In addition this `tenancy.key` can be rotated more easily
by using the new `tenancy:key:update` command.
- The `TenantAwareJob` trait has been completely removed. Instead before dispatching any Job into the queue it will identify
possible active tenants and remember/unserialize it when processing the job. You can still manually set the tenant website
by giving the property `website_id` on the Job a value before dispatching it.


## New features

- Added a `tenancy:key:update` command which allows rotating the `tenancy.key` value more easily.

## Minor

- Moved forcing app url to listener, so it applies to console and queue too.
- ConfigurationLoading, -Loaded events now know about current active tenant website.
- Allow configurable database privileges for tenant databases.
- Fixed website filter on commands.
- Improved hostname validation. Preventing common errors with ports and incorrect FQDN's.
- Website related events now also contain hostname in case one was identified.
- Upgraded to phpunit 8.

See the [changelog](https://github.com/hyn/multi-tenant/blob/5.x/changelog.md) for
all modifications.
