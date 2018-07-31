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

# Modularity

Back when hyn versions made huge leaps - while much of the interaction with Laravel was
mostly a matter of research, trial and error - I decided to drop a package that
added an admin dashboard in order to focus on creating a fundamentally better package.

In addition as new Laravel versions were released in pretty fast succession, new versions
(3.x, 4.x and 5.x) of hyn were started to offer to these new versions (5.3, 5.4 and 5.5).
In the end, maintaining so many branches was a pain and the package was reduced to one,
single 5.x version; a monolith.

This has been very fruitful for the package, but it made deciding whether to add functionality
not closely related to multi tenancy a struggle. Tenancy has been set up to work with git subsplit;
the same way the Laravel framwork is maintained. One repository contains all packages, using
the subsplit command read-only package repositories are updated automatically.

With this set up developing, maintaining and testing existing and new packages becomes far easier,
it also reduces the overhead for contributors.

# Extensibility

Now that modularity is been taken care of, working on third party integration no longer feels like
a new burden to pick up. Planned native integrations for the future include:

- Laravel Spark.
- Laravel Horizon.
- Different permission packages.
- Voyager and possibly other admin toolkits.

Keep an eye on the [integrations][gh-integration] label on
GitHub, or [submit an integration request][gh-new-issue] if you have one!

[gh-integration]: https://github.com/tenancy/tenancy/labels/type%3Aintegration
[gh-new-issue]: https://github.com/tenancy/tenancy/issues/new
