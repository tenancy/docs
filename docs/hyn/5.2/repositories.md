---
title: Repositories
icon: fal fa-dolly-flatbed
---

Tenancy is heavily driven by events. For event listeners to properly work, you
have to use the repositories to create new websites and hostnames.

# Create website

```php
use Hyn\Tenancy\Models\Website;
use Hyn\Tenancy\Contracts\Repositories\WebsiteRepository;

$website = new Website;
app(WebsiteRepository::class)->create($website);
dd($website->uuid); // Unique id
```

## UUID

Each website will be given a unique identifier automatically. To prevent
guessing URLs and to add a slight increased level of security this is a
random hash. 

The UUID is used for the database username and database name. In addition
I'd recommend using this unique string for every public facing URL, eg
an admin dashboard.

By default tenancy will create a `uuid` automatically
using a UUID generator, you can replace that class in the 
`tenancy.php > website > random-id-generator`, make sure it implements
`Hyn\Tenancy\Contracts\Website\UuidGenerator`.

If you want to force your own UUID for the website, without creating a full
fledged UUID generator, set the `uuid` property of your website before passing
it to the repository. 

```php
$website = new Website;
// Implement custom random hash using Laravels Str::random
$website->uuid = str_random(10);
app(WebsiteRepository::class)->create($website);
dd($website->uuid); // Random string of length 10
```

## Managing connection

Whenever you are using multiple databases, to separate tenants over different
regions for instance, you can easily specify the connection with which tenancy
should create, update and delete tenant databases and users.

```php
$website = new Website;
$website->managed_by_database_connection = 'system.asia';
app(WebsiteRepository::class)->create($website);
// Information of website is stored in the default system connection,
// the tenant database is created using the system.asia connection.
```

> Make sure the provided connection name is configured in your `config/database.php`.

# Create and connect hostname

```php
use Hyn\Tenancy\Models\Hostname;
use Hyn\Tenancy\Contracts\Repositories\HostnameRepository;

$hostname = new Hostname;
$hostname->fqdn = 'luceos.demo.app';
app(HostnameRepository::class)->attach($hostname, $website);
dd($website->hostnames); // Collection with $hostname
```

# Switch to new tenant

```php
use Hyn\Tenancy\Environment;
$tenancy = app(Environment::class);

$tenancy->hostname($hostname);

$tenancy->hostname(); // resolves $hostname as currently active hostname

$tenancy->tenant($website); // switches the tenant and reconfigures the app

$tenancy->website(); // resolves $website
$tenancy->tenant(); // resolves $website

$tenancy->identifyHostname(); // resets resolving $hostname by using the Request
```

# Querying

In case you would like to query the database for hostnames or websites. Use the
`query()` method of the repositories, this will return a [`Illuminate\Database\Eloquent\Builder`][query-builder]
instance.

[query-builder]: https://laravel.com/docs/5.6/queries
