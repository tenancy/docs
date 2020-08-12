---
title: General Setup
icon: fal fa-id-card-alt
excerpt: >
    General setup for tenant identification.
tags:
    - tenant
    - identification
---

# Tenant Identification

- [Overview](#overview)
- [Handling Nothing Identified](#handling-nothing-identified)
- [Next Steps](#next-steps)
  - [Lifecycle Hooks](#lifecycle-hooks)
  - [Identification Drivers](#identification-drivers)

## Overview

The tenant identification procedure decides which tenant you are trying to service. Although using the `TenantResolver` isn't absolutely necessary, doing so offloads a great deal of the tenancy business logic to the application.

The `TenantResolver` dispatches a few events used by the identification drivers to identify the currently requested [Tenant](tenant-what-is). By using an event you are not limited to only one driver. Each driver will only trigger it's own contract when it is identifying. However, if identification is triggered without a specific driver, it will try to identify all drivers. The first driver to respond with a valid tenant object will cause the other drivers to be ignored.

> Before making any changes to any code, it is recommended that you read though this entire document first.

## Handling Nothing Identified

Depending on how you choose to build out your application, there may be times where you need to perform 
specific logic when no tenant is identified. To achieve this one simply needs to create a listener for the `Tenancy\Identification\Events\NothingIdentified` event.

In the following example we will abort execution and return a 404 error.

```php
namespace App\Listeners;

use Tenancy\Identification\Events\NothingIdentified;

class NoTenantIdentified 
{
    public function handle(NothingIdentified $event) 
    {
        abort(404);
    }
}
```

> Note: This is only required if your application always expects to have a tenant identified and does not have a default non-tenant side.

## Next Steps

### Identification Drivers

Every driver uses the same principle. In order for a tenant to be used by the driver it has to implement an interface (or in Laravel terminology contract) related to the driver.

Depending on your application, you may need to identify a Tenant using different methods. The following Identification Drivers are available to be used alone, or in combination.

[Console Identification](identification-console)

[Environment Identification](identification-environment)

[HTTP based Identification](identification-http)

[Queue Identification](identification-queue)

> The identification driver will only attempt identification of a tenant model when it implements the contract of the driver.
