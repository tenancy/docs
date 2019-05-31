---
title: Concepts
icon: fal fa-vials
---

# Introduction

The principle of multi tenancy is about a single instance of software running
on a server and serving multiple tenants. The interpretation of such a tenancy
application is quite diverse.

Hyn tenancy is focused on providing a drop-in solution for Laravel without
sacrificing flexibility or requiring extensive hacks of the Laravel ecosystem.

Make sure you understand that the `Website` model is the subject of tenancy.

The default method for identifying such a tenant website is by using the requested
domain aka `hostname`. Taking a URL `http://yourdomain.com/foo/bar` only `yourdomain.com`
is taken into account, although modifications to this logic are easy to implement. The
`yourdomain.com` part of the URL is called a Fully Qualified Domain Name or `fqdn` for short.
 
A website can have zero or more hostnames connected to it. This allows for easier
attachment of additional hostnames or serving, for instance an ad campaign page,
on different domains.

# Website

You can think of a website as a single mirror of the code base, re-using your default
app code. In addition it is also possible to have very [tenant specific logic][directory-structure], 
for instance routes, a vendor folder, media and language files.

# Hostname

A hostname is a [Fully Qualified Domain Name][fqdn] (for instance `sub.example.com`).
Your application handles incoming requests to specific hostnames. Tenancy inspects
these requests and [sets up the tenancy environment][identification] according to a 
matching hostname or falls back to the default one.

# Runtime

By default, the package will identify the current requested `hostname` and bind it
into the `Hyn\Tenancy\Contracts\CurrentHostname` contract. In case that `hostname` belongs to
a website, the latter will be bound into the `Hyn\Tenancy\Contracts\Tenant` contract. Whenever
a Tenant is identified or switched the package will automatically infuse additional functionality
into Laravel, including [global tenant routes][routes], [tenant overrides][directory-structure] 
and [queues][queues].

[directory-structure]: structure
[identification]: identification
[routes]: fallback#tenant-routes-override
[queues]: queues
[fqdn]: https://www.godaddy.com/garage/industry/tech-svcs/it/whats-a-fully-qualified-domain-name-fqdn-and-whats-it-good-for/

