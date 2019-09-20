---
title: Commands
icon: fal fa-screwdriver
---

Aside from the tenant aware [migration](migrations) command, the following
is a list of additional commands provided by this package.

# Run proxy command

If you'd like to run any command for a tenant, you can use the `tenancy:run` command. 

You can execute and check the signature with

```bash
$ php artisan tenancy:run --help
```

How it works:

- Using the `--tenant` option is optional. If you leave it out the command will be run on all tenants.
- The `--tenant` option accepts either a single website id or a comma separated list of website ids. 
- If your target command requires options or arguments, you can add them like this:

```bash
$ php artisan tenancy:run <command> --option="key=value" --option="live=1" --argument="key=value"
```
> Make sure you include the argument or option key!

Example:

Execute Artisan command permission:update for website id 2 and 3

```bash
php artisan tenancy:run permission:update --tenant=2,3
```
Please note this command does not do any checking and will forward any exceptions it hits.

Also, **don't** run tenancy:run like this:

```bash
$ php artisan tenancy:run tenancy:run --argument="run=tenancy:run"
```

> Note: `tenancy:run` should not be called when running migrations. Use the `tenancy:migrate` command instead.

# Programmatic Artisan calls

```php
Artisan::call('tenancy:run', [
        'run' => 'permission:update',
        '--tenant' => [1,2,3]
        ]);
```
> Make sure --tenant value is always an array. An integer value will fail.

# Recreate command

The recreate command allows you to create databases and filesystem directories for
all tenants. It will check whether each tenant database already exists, create it if necessary
and then dispatch a "Created" event that causes the folders and possible vhost configurations
to be made.

```bash
$ php artisan tenancy:recreate
```

# Tenant Aware Tinker Command

This section describes how to enable tenant awareness for the native Laravel `tinker` command.

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
You can only tinker on one database at a time. Therefore the `website_id` needs to be a required option. Utilizing the `AddWebsiteFilterOnCommand` trait available in the Tenancy package would not work in this instance as it is tailored to run processes on multiple websites and makes the `website_id` parameter optional.

Second, create a new console command `TinkerCommand` that uses the trait above.

```php
<?php

namespace App\Console\Commands;

use Laravel\Tinker\Console\TinkerCommand as BaseCommand;
use App\Traits\MutatesTinkerCommand;

class TinkerCommand extends BaseCommand
{
    use MutatesTinkerCommand;
}
```

Now we have created a new `tenancy:tinker` artisan command. The `tenancy:tinker` requires the `--website_id=` option as a parameter. So, in order to run tinker on a `website_id` of 1, you’ll need to run:

```bash
$ php artisan tenancy:tinker --website_id=1

Running Tinker on website_id: 1
Psy Shell v0.9.6 (PHP 7.2.7 — cli) by Justin Hileman
>>>
```

It will throw an error if the `--website_id=` option is missing:

```bash
$ php artisan tenancy:tinker


  The tenancy website_id=0 does not exist.
```
