---
title: Identification driver queue
icon: fal fa-id-card-alt
excerpt: >
    Identifies tenants using the queue.
tags:
    - identification
    - console
    - driver
    - queue
---
Install using composer:

```bash
composer require tenancy/identification-driver-queue
```

In order to allow a tenant to be identified with the Queue driver,
you only need to install the package.

Any dispatched Job will then remember the last activated Tenant and
reactivate it when being processed. In case you want to manually
override the tenant, make sure to set the `$tenant_key` and `$tenant_identifier`
on the Job class before dispatching it.

```php
namespace App\Jobs;

use App\Customer;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Tenancy\Facades\Tenancy;

class MailCustomer implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    /** @var Customer $customer */
    public $customer;
    public $tenant_key = null;
    public $tenant_identifier = null;

    public function __construct(Customer $customer, $tenant_key = null, string $tenant_identifier = null)
    {
        $this->customer = $customer;
        $this->tenant_key = $tenant_key;
        $this->tenant_identifier = $tenant_identifier;
    }

    public function handle()
    {
        // ..
    }
}

```