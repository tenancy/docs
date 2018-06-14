---
title: Middleware
icon: fal fa-shield-alt
excerpt: Automatic actions per tenant
---
This package offers additional automation to make your life easier. Using
middleware, you can mutate how specific requests are handled.

# Maintenance

Set the property `under_maintenance_since` of the hostname to show a
Laravel generic maintenance page.

# Redirection

Set the property `redirect_to` to a valid URL to automatically redirect all
requests to this hostname.

# Secure

Set the property `force_https` to true to force all connections on this
hostname to be handled using a SSL encrypted connection.

# Abort

In case no tenant was identified the application will generate a default
not found page. You can disable this functionality globally in
the tenancy configuration file `hostname > abort-without-identified-hostname`.
