---
title: Pull requests
icon: fal fa-code-merge
---

We rely heavily on pull requests to our code base, not just for others but also ourselves. Using
pull requests leverages the knowledge and experience of the team and collaborators in order to
achieve the best result in terms of feature and code.

Everyone is different and therefor we won't expect you to comply to our complete list of requirements
for making a pull request. Nevertheless we will provide a list that gives an indication of what an
excellent pull request looks like. Doing so, helps understanding how we can speed up the process of
merging code into our projects.

## PR checklist

An **excellent** pull request:

- Provides sufficient **information**.
Understanding what exactly your pull request does is fundamental when reading the changes in the code. Ensure
your pull request has a clear description and offers specific questions for maintainers to look at.
- Relates to an **issue**.
Pull requests usually refer to an issue. Issues are used to discuss features, bugs and enhancements.
Including ways to solve them. Pull requests based off of issues often reduce the need to correct
the implementation.
- Has successful **checks**.
Each pull request will automatically dispatch our Continuous Integration pipeline. This pipeline will
run tests, find coding conventions errors and identify the change in code coverage. Satisfying these
checks will ensure we can merge sooner. 
- Implements or updates **tests**.
Our projects rely heavily on tests. New or updated functionality has to implement tests to confirm
it works during implementation and thereafter. Tests are a great way to understand how our package
breaks while refactoring or introducing new functionality. If you don't write the tests, we will
have to. 