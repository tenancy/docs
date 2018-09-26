---
title: Upgrading from 5.1
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

## Minor

- Fixed hardcoded tables in validators.

See the [changelog](https://github.com/hyn/multi-tenant/blob/5.x/changelog.md) for
all modifications.
