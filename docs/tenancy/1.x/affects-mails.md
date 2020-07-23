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
3. [Installation](#installation)
4. [Configuration](#configuration)
    1. [Example](#example)

## Overview

**Purpose**

The purpose of this package is to allow the sending of emails though a tenant's email address.

**Use Cases**

- TODO

**Events & Methods**

- `Tenancy\Affects\Mails\Events\ConfigureMails`
  - `replaceSwiftMailer`

> All other calls you do to the event, will be forwarded to the `Mailer `.

## Installation
Install through composer
```bash
composer require tenancy/affects-mails
```

### Configuring

TODO