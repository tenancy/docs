---
title: Configuration
icon: fal fa-sliders-v
---
# System connection

Tenancy needs a database connection with elevated permission to create databases
to be used by its tenants.

Make sure you set up this connection by following the instructions
in the [requirements][requirements], it also explains how to publish
the configuration files.

# Configuration Files

The inline documentation in both files should be self explanatory. Nevertheless,
I've listed some of the basic settings below.

## tenancy.php

The tenancy configuration file holds every setting related to tenants. This includes how to generate a unique
website, but also how database connections are set up. In addition it allows for;

- custom random id logic.
- manual tenant identification.
- on what disk (see `config/filesystems.php`) to store the tenant specific files.
- specifying tenant database division mode
- specifying the password generator
- specfiy the migrations path
- force models to a specific connection
- override your configs
- override your routes
- override translations
- override views
- and much, much more.

## webserver.php

With the webserver.php one can more closely fine tune integration of this package with your webserver.
Ports, vhost configuration files and the location to save these files are all contained in this config.

In general you can expect the following settings for each webserver;

- enabled; whether the service is enabled and thus the package will write files for it and reload the service.
- ports; which ports we should use, this is required to configure the vhost files properly.
- generator; the class handling the generation of vhost files.
- disk; the disk to write the vhost files to, you can have it point to any configured disk in your `config/filesystems.php`.
- paths; specific locations we need to know about to in order to mutate the webserver behavior.

[requirements]: requirements#elevated-database-user
