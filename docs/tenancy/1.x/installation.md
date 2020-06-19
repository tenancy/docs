---
title: Installation
icon: fal fa-arrow-alt-to-bottom
---
# Installation

1. [Simplified Install](#simplified-install)
2. [Selective Install](#selective-install) (*Recommended*)

## Simplified Install

For a quick, simplified installation you can install everything at once:

```bash
composer require tenancy/tenancy
```

> CAUTION: This will load specific non intrusive Affects by default. You can check which are loaded by looking in the `composer.json` of the repository.

This is an easy way of giving this toolkit a spin!

> We recommend selectively installing the packages you need.

After you've installed tenancy/tenancy make sure you register the Affects and Hooks you wish to use. You do this by simply registering the respective Providers.

## Selective Install

Instead of loading all tenancy packages at once, you can selectively install
what you need. You need at least the framework:

```bash
composer require tenancy/framework
```

After that selectively add:

- [Database drivers](database-drivers). Where to allocate tenant information.
- [Identification drivers](architecture-identification). Allowing you to configure how your tenant is identified.
- [Affects](architecture-affects). How your application is modified once a tenant is identified.
- [Lifecycle hooks](architecture-lifecycle). Impact your application whenever tenants are created, updated or deleted.
