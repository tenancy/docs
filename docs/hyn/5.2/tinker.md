---
title: Tinker
icon: fal fa-screwdriver
---

# Tenant Aware Tinker Command

This article describes how to enable tenant awareness for the native Laravel tinker command.

First, create a trait named `MutatesTinkerCommand` with the following content:

```php
<?php

namespace App\Traits;

use Hyn\Tenancy\Contracts\Repositories\WebsiteRepository;
use Hyn\Tenancy\Database\Connection;
use Illuminate\Database\Eloquent\ModelNotFoundException;
use Symfony\Component\Console\Exception\RuntimeException;
use Symfony\Component\Console\Input\InputOption;

trait MutatesTinkerCommand
{
    /**
     * @var Connection
     */
    private $connection;

    /**
     * @var WebsiteRepository
     */
    private $websites;

    public function __construct()
    {
        parent::__construct();
        $this->setName('tenancy:' . $this->getName());
        $this->websites = app(WebsiteRepository::class);
        $this->connection = app(Connection::class);
    }

    /**
     * Execute the console command.
     *
     * @return void
     *
     * @throws \Hyn\Tenancy\Exceptions\ConnectionException
     */
    public function handle()
    {
        $website_id = $this->option('website_id');
        try{
            $website = $this->websites->query()->where('id', $website_id)->firstOrFail();
            $this->connection->set($website);
            $this->info('Running Tinker on website_id: ' . $website_id);

            parent::handle();
            $this->connection->purge();
        } catch (ModelNotFoundException $e) {
            throw new RuntimeException(sprintf('The tenancy website_id=%d does not exist.', $website_id));
        }
    }

    protected function getOptions()
    {
        return array_merge([$this->addWebsiteOption()], parent::getOptions());
    }

    protected function addWebsiteOption()
    {
        return ['website_id', null, InputOption::VALUE_REQUIRED, 'The tenancy website_id (not uuid) to tinker specifically.', null];
    }
}
```
You can only tinker on one database at a time, therefore, the `website_id` needs to be a required option. Utilizing the `AddWebsiteFilterOnCommand` trait available in the Tenancy package would not work in this instance; it is tailored to run processes on multiple websites and makes the `website_id` parameter optional.

Second, create a new console command `TinkerCommand` that uses the trait above.

```php
<?php

namespace App\Console\Tinker;

use Laravel\Tinker\Console\TinkerCommand as BaseCommand;
use App\Traits\MutatesTinkerCommand;

class TinkerCommand extends BaseCommand
{
    use MutatesTinkerCommand;
}
```

Third, add the `TinkerCommand` to your Console Kernel `$commands` array.

```php
<?php

namespace App\Console;

use Illuminate\Console\Scheduling\Schedule;
use Illuminate\Foundation\Console\Kernel as ConsoleKernel;
use App\Console\Tinker\TinkerCommand;

class Kernel extends ConsoleKernel
{
    /**
     * The Artisan commands provided by your application.
     *
     * @var array
     */
    protected $commands = [
        TinkerCommand::class,
    ];
    ...
}
```

Now we have created a new `tenancy:tinker` artisan command. The `tenancy:tinker` requires the `--website_id=` option as a parameter. So, in order to run tinker on, let’s say with a `website_id` of 1, you’ll need to run:

`php artisan tenancy:tinker --website_id=1`

```sh
☁  app [development] ⚡  php artisan tenancy:tinker --website_id=1
Running Tinker on website_id: 1
Psy Shell v0.9.6 (PHP 7.2.7 — cli) by Justin Hileman
>>>
```

It will throw an error if the `--website_id=` option is missing:

```sh
☁  app [development] ⚡  php artisan tenancy:tinker


  The tenancy website_id=0 does not exist.


☁  app [development] ⚡
```
