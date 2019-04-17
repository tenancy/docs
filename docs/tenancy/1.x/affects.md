---
title: Affects
icon: fal fa-reply-all
excerpt: >
    How tenancy modifies your Laravel app.
tags:
    - affects
    - tenant
---
Affects are an important component to the workings of tenancy. Each affect influences your
Laravel app, but enabling them isn't enough for most of them. Affects fire events, which allow
you to hook into. This makes it incredibly easy to implement your own specific modifications
without the hard work of hooking into the Laravel ecosystem or knowing the inner workings of 
the tenancy framework.
