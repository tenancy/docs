---
title: Spatie media library
excerpt: A tutorial on setting up spatie/laravel-medialibrary.
tags:
    - spatie
    - medialibrary
    - tutorial
---
1. Create a fresh install of Laravel 5.7.  If you have installed the Laravel Terminal Installer run: 

```bash
$ laravel new app
```

2. `$ cd app`
3. Install `hyn/multi-tenant` package by running:
```bash
$ composer require hyn/multi-tenant
```
4. Follow the instructions until the "Deploy configuration" section.
5. If you are using MySQL as a database, make sure to add the entry `LIMIT_UUID_LENGTH_32=true` in your `.env` file.

## Install Spatie Medialibrary

Install this package via composer.
```bash
$ composer require spatie/laravel-medialibrary
```

Publish the migration with:
```bash
$ php artisan vendor:publish --provider="Spatie\MediaLibrary\MediaLibraryServiceProvider" --tag="migrations"
```

Move the migration file from `database/migrations/2018_10_10_085035_create_media_table.php` to `database/migrations/tenant/`.

After the migration has been published you can create the media-table in your tenant databases by running the migrations:
```bash
$ php artisan tenancy:migrate
```

You need to publish the config-file with:
```bash
$ php artisan vendor:publish --provider="Spatie\MediaLibrary\MediaLibraryServiceProvider" --tag="config"
```

## Integration

This section of the tutorial assumes that you have successfully [created at least 2 hostnames](creating-tenants)
and successfully have accessed them via your local environment setup.

We need to extend the `Media` eloquent model of the Medialibrary package so that we can add the
necessary changes we need in order for these two models to be tenant aware.

1. Create a Media.php file and place them inside your `app` directory,
next to your `User.php`. Make sure that these files extend `Spatie\MediaLibrary\Models\Media::class`.

`App\Media.php` - extends from `SpatieMedia` and uses the `UsesTenantConnection` trait.
```php
<?php

namespace App;

use Hyn\Tenancy\Traits\UsesTenantConnection;
use Spatie\MediaLibrary\Models\Media as SpatieMedia;

class Media extends SpatieMedia
{
    use UsesTenantConnection;
}
```

2. Prepare your model

To associate media with a model, the tenant aware model must implement the following interface and trait:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Spatie\MediaLibrary\HasMedia\HasMedia;
use Hyn\Tenancy\Traits\UsesTenantConnection;
use Spatie\MediaLibrary\HasMedia\HasMediaTrait;

class User extends Model implements HasMedia
{
    use UsesTenantConnection, HasMediaTrait;
   ...
}
```

3. Modify your `config/medialibrary.php` file and modify the `media_model` that is being used here to point to your newly created model. e.g. `App\Media::class`

`config\medialibrary.php`
```php
<?php

return [

    /*
     * The fully qualified class name of the media model.
     */
    'media_model' => App\Media::class,

    ...
];
```

4. Attaching a media to your user model.

```php
<?php

use App\User;
use App\Role;
use App\Permission;
use Hyn\Tenancy\Models\Hostname;

// Retrieve your user model.
$user = App\User::find(1);

// Add a media from url and attach it to your model.
$user->addMediaFromUrl('http://url-to-your-media-to-attach.png')->toMediaCollection();

// Retrieve the media you just attached.
$media = $user->media->last();
```

That's about it. You can read more on how to customize the medialibrary on their website: [https://docs.spatie.be/laravel-medialibrary](https://docs.spatie.be/laravel-medialibrary)
