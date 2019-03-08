---
title: What is a tenant?
icon: fal fa-id-badge
excerpt: |
    How to determine the selection of your tenant, what are valid
    subjects and how do you configure these inside tenancy.
tags:
    - tenant
---

Whether to separate data based on the requested hostname, the logged
in user or the team the user belongs to, is entirely up to you.

The subject of tenancy, **the tenant**, can be any eloquent Model
in your application you wish to build your business logic around.

## The contract

The contract is required to mark a specific model as your tenant.
Simply have the model implement the method to get started:

```php
<?php

use Illuminate\Database\Eloquent\Model;
use Tenancy\Identification\Contracts\Tenant;

class User extends Model implements Tenant
{
    /**
     * The attribute of the Model to use for the key.
     *
     * @return string
     */
    public function getTenantKeyName(): string
    {
        return 'id';
    }

    /**
     * The actual value of the key for the tenant Model.
     *
     * @return string|int
     */
    public function getTenantKey()
    {
        return $this->id;
    }

    /**
     * The value type of the key.
     *
     * @return string
     */
    public function getTenantKeyType(): string
    {
        return 'int';
    }

    /**
     * A unique identifier, eg class or table to distinguish this tenant Model.
     *
     * @return string
     */
    public function getTenantIdentifier(): string
    {
        return get_class($this);
    }

    /**
     * Allows overriding the system connection used for the tenant.
     *
     * @return null|string
     */
    public function getManagingSystemConnection(): ?string
    {
        return null;
    }
}
```

This will force your User model to implement some methods
required for tenancy to do its work. 

## The trait

Of course tenancy offers an easy way of applying the methods
required by the contract to the model by re-using Laravel
functionality:

```php
<?php

use Illuminate\Database\Eloquent\Model;
use Tenancy\Identification\Concerns\AllowsTenantIdentification;
use Tenancy\Identification\Contracts\Tenant;

class User extends Model implements Tenant
{
    use AllowsTenantIdentification;
}
```
