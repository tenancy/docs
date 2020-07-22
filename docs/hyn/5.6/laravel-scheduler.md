---
title: Laravel Scheduler
excerpt: A tutorial on setting up Laravel's Scheduler to be Tenant Aware.
icon: fal fa-file-archive
tags:
    - laravel
    - scheduler
    - tutorial
---
# Introduction

Laravel's command scheduler allows you to fluently and expressively define your command schedule within Laravel itself. When using the scheduler, only a single Cron entry is needed on your server.

Your task schedule is defined in the `app/Console/Kernel.php` file's `schedule` method.

By making Laravel's Scheduler tenant aware, we will unlock the possibilty to run multiple commands on all our tenants, without having to worry about setting numerous Cron jobs on your server.

# Prerequisite
You need a fresh Laravel app with Hyn Multi-Tenant installed. Briefly here are the basics:

1. Create a fresh install of Laravel.  If you have installed the Laravel Terminal Installer run: `$ laravel new app`
2. `$ cd app`
3. Install `hyn/multi-tenant` package by running:

```bash
composer require hyn/multi-tenant
```

4. Follow the [installation instructions](https://tenancy.dev/docs/hyn/5.6/installation).

# Create a Command

To create a new command, use the `make:command` Artisan command. This command will create a new command class in the `app/Console/Commands` directory

```bash
php artisan make:command ExampleCommand
```

We are going to make this command Tenant Aware. We need to change the signature to accept a `website_id` option:

```php
protected $signature = 'example:command {--website_id=}';
```

We will take tenant awareness code from [Tenant Aware Tinker Command](https://tenancy.dev/docs/hyn/5.6/commands#tenant-aware-tinker-command). The ExampleCommand should now look like this:

```php
namespace App\Console\Commands;

use Illuminate\Console\Command;
use Hyn\Tenancy\Contracts\Repositories\WebsiteRepository;
use Hyn\Tenancy\Database\Connection;
use Illuminate\Database\Eloquent\ModelNotFoundException;
use Symfony\Component\Console\Exception\RuntimeException;

class ExampleCommand extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'example:command {--website_id=}';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Example Command - Tenant Aware';

    /**
     * @var Connection
     */
    private $connection;

    /**
     * @var WebsiteRepository
     */
    private $websites;

    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
        $this->websites = app(WebsiteRepository::class);
        $this->connection = app(Connection::class);
    }

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle()
    {
        $website_id = $this->option('website_id');
        try{
            $website = $this->websites->query()->where('id', $website_id)->firstOrFail();
            $this->connection->set($website);
            $this->info('Running Command on website_id: ' . $website_id);

            $this->tenantHandle();

            $this->connection->purge();
        } catch (ModelNotFoundException $e) {
            throw new RuntimeException(
                sprintf(
                    'The tenancy website_id=%d does not exist.',
                    $website_id
                )
            );
        }
    }

    /**
     * Execute the console command. Tenant Aware.
     *
     * @return mixed
     */
    public function tenantHandle()
    {
        // You are now Tenant Aware
        // Execute something, dispatch a Job, or anything else
    }
}
```

# Integration

>This section of the tutorial assumes that you have successfully created one or more tenant aware commands.

We now need to register our `ExampleCommand` into our schedule. Laravel's Scheduler is set into `app/Console/Kernel.php`.

1. First, we need to tell Laravel which commands will be used in the scheduler

```php
protected $commands = [
    ExampleCommand::class,
    MySecondCommand::class,
    ...
];

```

2. Create a method for each command you need to run. We will be using the `before` [Task Hook](https://laravel.com/docs/7.x/scheduling#task-hooks) to specify code to be executed before the scheduled task is launched. Here is an example:

```php
/**
 * Example Command Schedule
 * @param  Illuminate\Console\Scheduling\Schedule   $schedule   Instance of schedule
 * @return void
 */
protected function scheduleExampleCommand(Schedule $schedule)
{
    // Set the Scheduler to our Tenant Aware Command. Store the returned Event.
    $scheduledEvent = $schedule->command(ExampleCommand::class);

    // Init a new variable to alter the Event's command string
    $commandString = null;

    // Setup your schedule
    $scheduledEvent
        // Setup our `before` Hook
        ->before(function () use ($scheduledEvent, &$commandString) {
            // Retreive current tenant
            $tenant = app(\Hyn\Tenancy\Environment::class)->tenant();

            // Retreive and store the inital generated command. See note below*
            if ($commandString === null) {
                $commandString = $scheduledEvent->command;
            }

            // Replace the Event's generated command
            // Adding our tenant's `website_id` option
            $scheduledEvent->command = sprintf(
                "%s --website_id=%d",
                $commandString,
                $tenant->id
            );
        })

        // Define your schedule as you would normally do
        //->everyHour()
        //->runInBackground()
        //->evenInMaintenanceMode();
}
```
Your can find all available schedule options on [Laravel's Official Documentation](https://laravel.com/docs/6.x/scheduling#schedule-frequency-options) page.

>**Note: Without this, `--website_id` option would be added one after the other.*


3. Set our `schedule` method to run our new methods. Use the `Tenancy\Environment` to define whether you're running with `tenancy:run` or not.

```php
/**
 * Define the application's command schedule.
 *
 * @param  \Illuminate\Console\Scheduling\Schedule $schedule
 * @return void
 */
protected function schedule(Schedule $schedule)
{
    if (app(\Hyn\Tenancy\Environment::class)->tenant()) {
        // Run your tenant aware schedules
        $this->scheduleExampleCommand($schedule);
        $this->scheduleMySecondCommand($schedule);
        ...
    } else {
        // Run your system schedules
        // ...
    }
}
```

4. Setup your server Cron

```bash
# Tenant
* * * * * cd /path-to-your-project && php artisan tenancy:run schedule:run >> /dev/null 2>&1

# System
# * * * * * cd /path-to-your-project && php artisan schedule:run >> /dev/null 2>&1
```
