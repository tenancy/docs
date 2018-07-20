---
title: Compared to hyn
icon: fal fa-box-check
---

The predecessor, `hyn/multi-tenant` ("hyn"), to this package ("tenancy") 
was created with a different purpose in mind. Originally the intent of my multi tenant 
package was to offer digital media companies a fast and easy way to set up 
websites for all customers, even when each of these were serving different apps. 
This type of multi tenancy has been slowly replaced with the Software as a Service we see nowadays.

# Local dependencies

The challenges of modern SaaS are not related to offering additional code
using dynamically, composer auto loaded packages. Nor do they rely on
local filesystems. The successor to hyn `tenancy/tenancy` ("tenancy") aims
solely on apps living in the cloud using scalable architecture like block
storage, load balancing and cloud databases.

Functionality offered in hyn which rely on local filesystems are therefor
no longer a default feature. This includes most (if not all) overrides
from files living inside the tenant directory.

# Tenant identification

Although hyn was never intended to be opinionated about the choices of
the developer - and, trust me, I've made a few changes to improve that position -
it would still use a hostname and website model to relate to a tenant. 

Initial confusion arose while binding the hostname, identified from the current request,
into the service container as a tenant. In version 5.2 of hyn a correction
was introduced binding a website model to a new tenant contract.

In the end, forcing any developer to use the requested host for identification, while
using other implementations to identify the tenant inside, for instance, artisan commands
doesn't make a lot of sense. Tenant identification should be contained inside one
specific shard of code. The `TenantResolver` class in tenancy offers a centrally
organised method of identifying tenants; event listeners can respond with a
tenant compliant model. The first listener to provide a valid tenant model "wins",
switching the current environment.
