---
title: Upgrading from 5.2
icon: fal fa-level-up-alt
---
The 5.3 adds compatibility with Laravel 5.7 while fixing minor issues and adding
more features.

## Backward breaking changes

- The configuration `tenancy.hostname.abort-without-identified-hostname` is
now default false.

## New features

- Added a `tenancy:run` command that allows using any other artisan command within
one or more tenant-enabled environments.
- Multi schema support for PostgreSQL.

## Minor

- Fixed hardcoded tables in validators.
- `route()` helper now is able to generate urls when using the `routes/tenants.php` file.
- Fixed grants being given on users we weren't creating.
- More minor bug fixes and improvements.

See the [changelog](https://github.com/hyn/multi-tenant/blob/5.x/changelog.md) for
all modifications.
