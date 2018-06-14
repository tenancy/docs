---
title: Concepts
icon: fal fa-vials
---

# Introduction

The principle of multi tenancy is about a single instance of software running
on a server and serving multiple tenants. The interpretation of such a tenancy
application is quite divers, the possible implementations is even without
boundary.

Hyn tenancy is focused to provide a drop-in solution for Laravel without
sacrificing flexibility or hacks of the Laravel ecosystem.

This packages holds onto one core belief though; **the tenant is the website**, so;

- A tenant is a website.
- A website can have zero or more hostnames.
    - A website is identified when a specific hostname is requested.

# Website

You can think of a website as a single mirror of the code base, re-using your default
app code. In addition it is also possible to have very [tenant specific logic][directory-structure], 
for instance routes, a vendor folder, media and language files.

# Hostname

A hostname is a [Fully Qualified Domain Name][fqdn] (for instance sub.example.com).
Your application handles incoming requests to specific hostnames. Tenancy inspects
these requests and [sets up the tenancy environment][identification] according to a 
matching hostname or default fallback.


[directory-structure]: structure
[fqdn]: https://www.godaddy.com/garage/industry/tech-svcs/it/whats-a-fully-qualified-domain-name-fqdn-and-whats-it-good-for/
[identification]: identification
