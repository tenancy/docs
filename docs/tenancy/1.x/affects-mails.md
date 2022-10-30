---
title: Mails
icon: fal fa-envelope
excerpt: >
    How you can easily affect the mails in your Laravel application.
tags:
    - affects
    - tenant
    - mails
---

# Affects-Mails

1. [Overview](#overview)
2. [Installation](#installation)
3. [Configuration](#configuration)
    1. [Example](#example)

## Overview

**Purpose**

The purpose of this package is to allow the sending of emails though a tenant's email address.

**Use Cases**

- Send emails from a tenant's email server
- Send emails from different providers

**Events & Methods**

- `Tenancy\Affects\Mails\Events\ConfigureMails`
  - `replaceSymfonyTransport`

> All other calls you do to the event, will be forwarded to the `Mailer`.

## Installation

### Using Tenancy/Framework
Install via composer:
```bash
composer require tenancy/affects-mails
```

### Using Tenancy/Tenancy or with provider discovery disabled
Register the following ServiceProvider: 
  - `Tenancy\Affects\Mails\Provider::class`

## Configuration
After the installation of the package, all you have to do is configure the package in the way you want. Like most affects, this package will fire an event `Tenancy\Affects\Mails\Events\ConfigureMails`, which will provide you with some functionality to change or update the `TransportInterface` used:
- `replaceSymfonyTransport()`, this will change the default mailer to the one provided.

> All the calls you do to the event, will be forwarded to the `Mailer`.

### Example
In the following example we will change the 'from' address on the emails that are sent.
We will assume that every tenant has two additional fields called `email_from` and `email_name` to store what email and display name should be used for outgoing email.
```php
namespace App\Listeners;

use Tenancy\Affects\Mails\Events\ConfigureMails;

class ConfigureTenantMails
{
    public function handle(ConfigureMails $event)
    {
        if($tenant = $event->event->tenant)
        {
            // Check if the alwaysFrom method exists: it does not exists on Laravel's fake
            if(method_exists($event->mailer, 'alwaysFrom'))
            {
                // Set the "From" Field
                $event->alwaysFrom($tenant->email_from, $tenant->email_name);
            }
            
            // Check if the alwaysReplyTo method exists: it does not exists on Laravel's fake
            if(method_exists($event->mailer, 'alwaysReplyTo')
            {
                // Optionally add the "ReplyTo" Field
                $event->alwaysReplyTo($tenant->mail_replyto, $tenant->mail_replyto_name);
            }
        }
    }
}
```
