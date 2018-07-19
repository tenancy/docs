---
title: Logging
icon: fal fa-book
---

Especially in production you might want to split the default laravel log by tenants.
Tenancy ships with a custom tenant aware logger which you can register in the logger config.

- If a tenant is detected it will create a `logs` folder within the tenant directory:
`storage/app/tenancy/tenants/<tenant uuid>/logs/<log_level>_<YYYY>-<MM>-<DD>.log`
- If no tenant is detected it will store the log file to:
`storage/logs/`.

Add a new `tenant` channel to the channels array in your **config/logger.php** like below:

```php
// ...
'channels' => [
        // ...
        'tenant' => [
            'driver' => 'custom',
            'via' => \Hyn\Tenancy\Logging\TenantAwareLogger::class,
            'level' => 'debug',
        ],
// ...
```

Then just change the 'single' channel to 'tenant' channel your stack channel:

```php
// ...
'stack' => [
            'driver' => 'stack',
            'channels' => ['tenant'],
        ],
// ...
```




