---
title: Fallback
excerpt: Configure a landing page using different approaches
icon: fal fa-barcode-scan
---

There are several ways to organize a fallback domain. These are commonly used to offer
a landing page or admin area for your clients to configure their tenant website.

# Create a tenant as fallback

One of the easiest methods is to simply seed a default tenant that has a specific
hostname. Set the environment `TENANCY_DEFAULT_HOSTNAME` to the exact FQDN used
for that tenant.

Now every time no other tenant was identified, it will revert to this tenant. A
drawback of this method is the need to set up a tenant website.

# Disabling abort to pass through

Disable in the `tenancy.php` configuration file the `abort-without-identified-hostname` option
by setting it to `false`. This will prevent tenancy from throwing a 404 (not found) whenever
it failed to identify a tenant.

Depending on how you set up your app, the code will now pass through to any registered routes.
For applications that have global routes defined for tenant specific functionality, you'll
run into the issue that no "tenant" connection was defined.

An easy solution to this is writing a custom middleware that sets up the tenant database configuration
for the specific landing page site. Something like the following might already be sufficient;

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class FallbackHandler
{
    public function handle(Request $request, Closure $next)
    {
        if ($request->getHost() === env('FALLBACK_HOST')) {
            config(['database.connections.tenant' => [
                'driver' => 'mysql',
                'host' => env('DB_HOST', '127.0.0.1'),
                'port' => env('DB_PORT', '3306'),
                'database' => env('DB_DATABASE', 'forge'),
                'username' => env('DB_USERNAME', 'forge'),
                'password' => env('DB_PASSWORD', ''),
                'unix_socket' => env('DB_SOCKET', ''),
                'charset' => 'utf8mb4',
                'collation' => 'utf8mb4_unicode_ci',
                'prefix' => '',
                'strict' => true,
                'engine' => null,
            ]);
        }    
    
        return $next($request);
    }
}
```

An alternative could be to extend the hostnames table to add a flag and use that flag in
the above middleware to signify it should re-use (or set up) a specific connection.
