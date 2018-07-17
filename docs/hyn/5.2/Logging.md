---
title: Logging
icon: fal fa-book
---

Especially in production you might want to split the default laravel log by tenants.
Tenancy shipps with a custom tenant aware logger which you can register in the logger config.

If a valid hostname is detected it will create a `logs` folder within the tenant directory ie.:
`app/tenancy/tenants/xxxxxxxxxxxxxxxxxxxx/logs/{log_level}_xxxx-xx-xx.log`

If no valid hostname is detected it will default to:
`/storage/logs/{log_level}_xxxx-xx-xx.log`

Add a new `tenant` channel to the channels array in your **config/logger.php** like below:

```php
...
'channels' => [
        ...
        'tenant' => [
            'driver' => 'custom',
            'via' => Hyn\Tenancy\Logging\TenantAwareLogger::class,
            'level' => 'debug',
        ],
...
```

Then just change the 'single' channel to 'tenant' channel your stack channel:

```php
'stack' => [
            'driver' => 'stack',
            'channels' => ['tenant'],
        ],
...
```




