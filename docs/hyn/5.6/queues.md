---
title: Queues
icon: fal fa-conveyor-belt
---

One of the strongest features of Laravel is its queues. One drawback of sending
Jobs into the Queue is that these are executed in a completely different process
depending on your queue configuration, including redis and beanstalk.

In order to assist you with tenant aware jobs, this package automatically identifies
whether a tenant website is active before processing the job. This ensures that
jobs are always tenant aware. In case you wish to override the tenant for which the job
is executed, make sure to set the `website_id` property on the Job before dispatching it.

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\SerializesModels;

class ProcessPodcast implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
    
    public $website_id;
    
    public function __construct(int $website_id) {
        $this->website_id = $website_id;
    }
}
```
