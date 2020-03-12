---
title: Testing
icon: fal fa-vial
---

## Introduction

To run tests on your multi-tenant codebase, you will require a new tenant to be created every test. The best way to do this is via an in-memory SQLite database.

## Considerations

There are pros and cons to testing with MySQL vs. SQLite. As SQLite is the faster option, it's the one we'll be using here, but for reference, pros and cons for both are here too:

MySQL:
- Pros:
  - Fully featured migrations with no compromises
  - Accessible via a SQL editor for debugging
- Cons:
  - Extremely slow tests due to the sheer amount of queries generated
  - If using the `prefix` division driver, you may run into duplicate foreign key errors
  - If using the `database` division driver, you will have to manually clean up databases if a test fails with an error
  - CI servers will require MySQL installed

In-memory SQLite:
- Pros:
  - Extremely fast, sub-1000ms tests
  - If a test fails with an error, there's no leftover databases to manually clean up
  - CI does not require a MySQL server running
- Cons:
  - Migrations will require some rewriting and careful consideration in the future

## Migration Caveats

Make yourself familiar with the already documented caveats in the [Laravel documentation](https://laravel.com/docs/migrations).

You can drop multiple columns in a single migration file, but not a single migration closure.

```php
// incorrect
Schema::table('users', function (Blueprint $table) {
    $table->dropColumn('location');
    $table->dropColumn('biography');
});

// correct
Schema::table('users', function (Blueprint $table) {
    $table->dropColumn('location');
});
Schema::table('users', function (Blueprint $table) {
    $table->dropColumn('biography');
});
```

You cannot drop a column at the beginning of a migration closure, or else the rest of the migration will silently fail.

```php
// incorrect
Schema::table('users', function (Blueprint $table) {
    $table->dropColumn('location');     // migrates successfully
    $table->integer('age')->nullable(); // will not migrate
});

// correct
Schema::table('users', function (Blueprint $table) {
    $table->integer('age')->nullable();
    $table->dropColumn('location');
});

// also correct
Schema::table('users', function (Blueprint $table) {
    $table->dropColumn('location');
});
Schema::table('users', function (Blueprint $table) {
    $table->integer('age')->nullable();
});
```

## Boilerplate

### tests/Helpers/SqliteDriver.php

```php
<?php

namespace Tests\Helpers;

use Hyn\Tenancy\Contracts\Webserver\DatabaseGenerator;
use Hyn\Tenancy\Contracts\Website;
use Hyn\Tenancy\Database\Connection;
use Hyn\Tenancy\Events\Websites\Created;
use Hyn\Tenancy\Events\Websites\Deleted;
use Hyn\Tenancy\Events\Websites\Updated;

class SqliteDriver implements DatabaseGenerator
{
    public function created(Created $event, array $config, Connection $connection): bool
    {
        return true;
    }

    public function updated(Updated $event, array $config, Connection $connection): bool
    {
        return true;
    }

    public function deleted(Deleted $event, array $config, Connection $connection): bool
    {
        return true;
    }

    public function updatePassword(Website $website, array $config, Connection $connection): bool
    {
        return true;
    }
}
```

### tests/TenantAwareTestCase.php

```php
<?php

namespace Tests;

use Hyn\Tenancy\Models\Website;
use Hyn\Tenancy\Contracts\Repositories\WebsiteRepository;
use Hyn\Tenancy\Models\Hostname;
use Hyn\Tenancy\Contracts\Repositories\HostnameRepository;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\TestCase;
use Hyn\Tenancy\Providers\Tenants\RouteProvider;
use Hyn\Tenancy\Contracts\CurrentHostname;
use Tests\Helpers\SqliteDriver;
use Tests\CreatesApplication;

class TenantAwareTestCase extends TestCase
{
    use CreatesApplication, RefreshDatabase;

    /**
     * @var string
     */
    protected $hostname = 'testing.test';

    /**
     * @var Website
     */
    protected $website;

    protected function setUp(): void
    {
        parent::setUp();

        // register SQLite database driver
        $this->app->make('tenancy.db.drivers')->put('sqlite', SqliteDriver::class);

        // bypass tenant connection settings modifications
        config(['tenancy.db.tenant-division-mode' => 'bypass']);

        // create a website
        $this->website = new Website;
        $this->app->make(WebsiteRepository::class)->create($this->website);

        // create a hostname
        $hostname = new Hostname;
        $hostname->fqdn = $this->hostname;
        $hostname = $this->app->make(HostnameRepository::class)->create($hostname);
        $this->app->make(HostnameRepository::class)->attach($hostname, $this->website);

        // set current hostname
        $this->app->singleton(CurrentHostname::class, function () use ($hostname) {
            return $hostname;
        });

        // register tenant routes, by default web/tenants.php
        (new RouteProvider($this->app))->boot();
    }
}
```

### config/database.php

```php
'system' => [
    'driver' => env('TENANCY_DRIVER', 'mysql'),
    'host' => env('TENANCY_HOST', '127.0.0.1'),
    'port' => env('TENANCY_PORT', '3306'),
    'database' => env('TENANCY_DATABASE', 'tenancy'),
    'username' => env('TENANCY_USERNAME', 'tenancy'),
    'password' => env('TENANCY_PASSWORD', ''),
    'unix_socket' => env('DB_SOCKET', ''),
    'charset' => 'utf8mb4',
    'collation' => 'utf8mb4_unicode_ci',
    'prefix' => '',
    'strict' => true,
    'engine' => null,
],
```

## phpunit.xml

Or if you use a separate testing `.env` file, set these values in there.

```xml
<env name="DB_CONNECTION" value="system"/>
<env name="TENANCY_DRIVER" value="sqlite"/>
<env name="TENANCY_DATABASE" value=":memory:"/>
<env name="TENANCY_DATABASE_DIVISION_MODE" value="database"/>
```

## Usage

```php
<?php

namespace Tests\Unit;

use Tests\TenantAwareTestCase;

class ExampleTest extends TenantAwareTestCase
{
    public function test_user_creation()
    {
        $user = \App\User::create([
            'name' => 'test',
        ]);

        $this->assertEquals('test', $user->name);
    }
}
```
