---
title: Events
icon: fal fa-hourglass-end
---

To create a fluid ecosystem around Laravel, this package uses events heavily.

I highly recommend reading up on the native [events][laravel-events] by Laravel
in case you are unaware about its purposes.

# Model events

Namespaces: 

- `Hyn\Tenancy\Events\Customers`
- `Hyn\Tenancy\Events\Hostnames`
- `Hyn\Tenancy\Events\Websites`

All of the models have the following events per default, these should be
self explanatory.

- Creating
- Created
- Updating
- Updated
- Deleting
- Deleted

## Hostnames

Namespace: `Hyn\Tenancy\Events\Hostnames`.

The hostname models has a few additional events;

- Attached; the hostname was attached to a website.
- Detached; the hostname was removed from a website.
- Identified; the hostname is identified based on the request.
- NoneFound; no hostname was found during identification.
- Redirected; the hostname is configured to redirect to another url.
- Secured; the hostname is configured to upgrade the request to https.
- Switched; currently active tenant was changed.
- UnderMaintenance; the hostname is configured to be in maintenance.

# Database

Namespace: `Hyn\Tenancy\Events\Database`.

- Creating
- Created
- Deleting
- Deleted
- Renaming
- Renamed
- ConfigurationLoading; the configuration for a tenant database connection
is loading.
- ConfigurationLoaded; the configuration for a tenant database connection
was loaded.

# Webservers

Namespace: `Hyn\Tenancy\Events\Webservers`.

- ConfigurationSaved; the configuration for the webserver was saved to disk.

[laravel-events]: https://laravel.com/docs/5.5/events
