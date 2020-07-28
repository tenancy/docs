---
title: Architecture
icon: fa fa-cubes
excerpt: |
    An introduction to the architecture of Tenancy.
---

# Architecture

The architecture of Tenancy is broken up into three main component types. 
- Lifecycle Hooks
- Identification
- Affects

## Lifecycle Hooks
These components focus on what happens when a tenant is created, updated, or deleted in the system.
Things as simple as ensuring that the domain a tenant sign up with is valid, to creating databases and registering/creating services to handle the needs to the tenant occur in these components.

[Read More])(hooks-general)

## Identification
The identification components of tenancy are responible for identifing the tenant before application logic
occurs regardless if that logic stems from an HTTP request, a job running in a queue, or a command being run in the console.

[Read More])(identification-general)


## Affects
The Affect compoents are responsible for changing the application for the tenant that has been identified.
Setting up connections to databases, changing logging settings, and the configuration of other services
occur in these components.

[Read More])(affects-general)