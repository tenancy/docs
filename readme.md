# Documentation for Laravel Tenancy

These are the source files used on [laravel-tenancy.com](https://laravel-tenancy.com).

These files use YAML frontmatter, which means a docs files can have a YAML based meta section
followed by a CommonMark markdown string.

```md

---
title: Some title
tags:
    - important
---
Welcome to code and other **markdown**.
```

The docs are all categorized based on their packages and versions. Currently the docs supports
`hyn/multi-tenant` and the upcoming `tenancy/tenancy` packages.

In order to create a navigation element, the tree of these documentations are specified in the meta
file [docs.yaml](docs.yaml).
