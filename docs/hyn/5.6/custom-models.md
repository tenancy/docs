---
title: Custom system models
icon: fal fa-theater-masks
---

This package allows you to specify different models to be used for the 
Hostname and Website models. Tenancy will take care of the relationships on the 
package-end. You also need to implement `Hyn\Tenancy\Contracts\{Model}`

Take this example:

```php
<?php

namespace App;

use Hyn\Tenancy\Contracts\Website as Contract;

class Website implements Contract
{
}
```

All you need to do is update the `tenancy.php` configuration file:

```php
<?php
return [
  'models' => [
      // ..
      // Must implement \Hyn\Tenancy\Contracts\Hostname
      'hostname' => \Hyn\Tenancy\Models\Hostname::class,
      // Must implement \Hyn\Tenancy\Contracts\Website
      'website' => \App\Website::class
  ],
  // ...
];
```
