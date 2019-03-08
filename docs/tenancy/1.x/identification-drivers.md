---
title: Identification drivers
icon: fal fa-id-card-alt
excerpt: >
    Identification process explained, including the drivers and configuration of each of these.
tags:
    - identification
    - tenant
    - driver
---
The tenant identification procedure decides which tenant you are trying to service. Although
using the tenant resolver isn't absolutely necessary,
doing so offloads a great deal of the tenancy business logic to the package.

The tenant resolver dispatches a few events used by the identification drivers to identify the
currently requested [tenant][what-is-a-tenant]. By using an event you are not limited to only 
one driver. The first driver to respond with a valid tenant object will cause any following
listeners to be ignored.

- [http](identification-driver-http)
- [console](identification-driver-console)
- [environment](identification-driver-environment)

[what-is-a-tenant]: what-is-a-tenant
