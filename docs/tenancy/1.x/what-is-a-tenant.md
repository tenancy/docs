---
title: What is a tenant?
icon: fal fa-id-badge
excerpt: |
    How to determine the selection of your tenant, what are valid
    subjects and how do you configure these inside tenancy.
tags:
    - tenant
---

# What is a Tenant?

1. [Overview](#overview)
2. [Deciding on your Tenant](#deciding-on-your-tenant)
3. [Next Step](#next-step)

## Overview

Whether to separate data based on the requested hostname, the logged
in user or the team the user belongs to, is entirely up to you.

The subject of tenancy, **the tenant**, can be any eloquent Model
in your application you wish to build your business logic around.

## Deciding on your Tenant

The most important decision in building a multi tenant application
is choosing your tenant. In order to help you with this, ask yourself the
following questions.

To which entity in my business logic does the tenant data belong to?

Do I have companies with employees signing up? Then most likely that
company is the tenant, it holds all the information we want to keep separated
from any other company in the application. This is a very common
choice visible with applications using a vanity url, eg yourteam.saas.app.

Or perhaps we can keep it simple by marking the user as tenant. This will
connect all data to a single account instead. A great example of this is
GMail. You log in and see only the emails of your account.

A hybrid version is also well known, GitHub offers personal namespaces, for instance
github.com/luceos and team namespaces like github.com/tenancy.

Tenancy offers a solution for any scenario as there's no limitation to how
a tenant is identified, nor how many tenants can be configured. To give you an idea
using the hybrid example from above the tenants would be both a User model 
and an Organization model. They can be marked as a valid tenant entity by applying
a contract and implementing the required methods. 

## Next Step

Once you have identified what model(s) will be your tenant you will need to setup the identification of those tenants. To get started continue to [Tenant Identification](identification-general)