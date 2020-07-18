---
title: Validations
icon: fal fa-file-check
---

## Exists and Unique Validation Rule

Among all the built-in validation in Laravel, only the [exists](https://laravel.com/docs/7.x/validation#rule-exists) and [unique](https://laravel.com/docs/7.x/validation#rule-unique) validation rules interact with the database.

We can leverage these two rules with tenancy by adding the connection parameter. Both use the same validation rule
signature which is `rule:connection.table,column`.

So for example, to use `Exists (Database)` use `'email' => 'exists:tenant.staff,email'` where `tenant` is the `'tenant-connection-name'` you specified in your `tenancy.php` config file.

Similarly, you can substitute the `exists` keyword to `unique` to use the `unique` rule instead, eg: `'email' => 'unique:tenant.staff,email'`.

> Validating against the system database is possible using the method described above but replacing `tenant` with `system`.
