---
title: Upgrading from 5.1
icon: fal fa-level-up-alt
---
The 5.2 release cleans up the codebase in an attempt to clarify
the required models and the subject of tenancy. In addition
it offers even better integration for SaaS apps with the improved
global routes handling.

## Backward breaking changes

- The `tenancy:install` command has been removed, instead you'll have to
publish and/or run the migration files provided by the package using 
`php artisan vendor:publish --tag tenancy` and `php artisan migrate`.
- The `Customer` model has been dropped, make sure to copy it to your code base
before upgrading. There's no migration to drop the existing
tables, to prevent existing installations from losing stored data. You can
safely remove the tables with a migration of your own in case you've
never used the model in your app.
- As the documentation has been pointing out, the tenant in this package
has always been the Website class. However, due to legacy code, the identified
Hostname had become the subject of identification. In order to straighten this
out, from now whenever an identified Hostname is connected to a website, that
website is bound to the new `Tenant` contract. All logic related to connections,
custom tenant configuration files etc are now all following the Tenant Website.
- Using `Environment::hostname($hostname)` to set a hostname no longer switches
the tenant, you'll need to use `Environment::tenant($website)` for this now.
- The `TenantAwareJob` trait now uses the `website_id` and the fluent setter `onTenant()`.
- The `Hostname` and `Website` models now have the `SoftDeletes` trait applied.

## New features

- Setting the `tenancy.website.disk` configuration to `false` will fully disable 
all filesystem related functionality for tenants, including configs, vendor and 
translations.
- Creating a `routes/tenants.php` file now allows automatically overriding or replacing
global routes. This allows for far easier landing/fallback pages. Check the
`tenancy.php` config files under `routes`.
- The Connection class now offers an `exists($name)` method to check whether a
specific connection was configured.

## Minor

- Improved local testing of the package. All traces of the tests are removed.
- The clean-local-dbs bash script has been slightly improved to clean up the
local database better.
- Dropped all references to `$ssl` which was meant to be included somewhere in
version 2.x, let's encrypt has great binaries to solve the certificate question.
- `WebsiteRepository::findById` now allows both integer and string argument, which
Laravel natively allows.
