---
title: Installation
icon: fal fa-arrow-alt-to-bottom
---

For a quick, simplified installation you can install everything at once:

```bash
composer require tenancy/tenancy
```

This is an easy way of giving this toolkit a spin!

> We recommend selectively installing the packages you need.

## Framework

Instead of loading the framework and all drivers at once, you can selectively install
what you need. You need at least the framework:

```bash
composer require tenancy/framework
``` 

After that selectively add:

- [Database drivers](database-drivers).
- [Identification drivers](identification-drivers).
- [Affects](affects).
- [Lifecycle hooks](hooks).
