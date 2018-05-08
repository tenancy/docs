---
title: Validations
icon: fal fa-file-check
---

#### Form Request Validation

One section that people might be wondering is how to validate their tenant aware models against their respective database.

##### Exists and Unique Validation Rule
Among all the built-in validation rules in Laravel, only these two rules interact with the database.
You can see their usage [here](https://laravel.com/docs/5.6/validation#rule-exists) and [here](https://laravel.com/docs/5.6/validation#rule-unique)
respectively.

We can leverage these two rules with tenancy by adding the connection parameter. Both uses the same validation rule
signature which is `rule:connection.table,column`.

So for example, to use `Exists (Database)` use `'email' => 'exists:tenant.staff,email'`. You just need to change `exists` keyword to `unique` to use the `unique` rule instead.

> Whatever tenant database you are using you just need to change the connection to use the `tenant` connection. The package takes care of the rest.
