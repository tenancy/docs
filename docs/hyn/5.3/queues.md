---
title: Queues
icon: fal fa-conveyor-belt
---

One of the strongest features of Laravel are its queues. One drawback of sending
Jobs into the Queue is that these are executed in a completely different process
depending on your queue configuration including redis and beanstalk.

In order to assist you with tenant aware jobs, a `TenantAwareJob` is available to you.
Instead of applying the `SerializesModel` trait as per suggestion in the Laravel
documentation, you should use this Trait instead.

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Hyn\Tenancy\Queue\TenantAwareJob;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;

class ProcessPodcast implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, TenantAwareJob;
    
    // ..
}
```

Once you've applied this custom trait the currently active tenant website will be
parsed into the Job with property `$website_id`. During processing of the job
this property is read and the Environment is automatically setting that website
as active tenant.

In case you'd like to manually set a specific tenant for the job, use the `onTenant()`
method:

```php
dispatch((new ProcessPodcast($podcast))->onTenant($website));
```
