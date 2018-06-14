The 5.2 release cleans up the codebase in an attempt to clarify
the required models and the subject of tenancy.

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
- The `TenantAwareJob` trait now uses the website_id.
