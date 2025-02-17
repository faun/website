---
title: Upgrading Vitess
weight: 1
aliases: ['/docs/user-guides/upgrading/', '/docs/user-guides/upgrading-vitess/']
---

This document highlights things to be aware of when upgrading a Vitess production installation to a newer Vitess release.

Generally speaking, upgrading Vitess is a safe and easy process because it is explicitly designed for it. This is because at YouTube we followed the practice of releasing new versions often (usually from the tip of the Git primary branch).

## Compatibility

Our versioning strategy is based on [Semantic Versioning](http://semver.org/).

Vitess version numbers follow the format `MAJOR.MINOR.PATCH`. We guarantee compatibility when upgrading to a newer **patch** or **minor** version. Upgrades to a higher **major** version may require manual configuration changes.

In general, always **read the 'Upgrading' section of the release notes**. It will mention any incompatible changes and necessary manual steps.

## Upgrade Order

We recommend to upgrade components in a bottom-to-top order such that "old" clients will talk to "new" servers during the transition.

Please use this upgrade order (unless otherwise noted in the release notes):

* vttablet
* vtctld
* vtgate
* application code which links client libraries

## Canary Testing

Within the vtgate and vttablet components, we recommend to [canary](http://martinfowler.com/bliki/CanaryRelease.html) single instances, keyspaces and cells. Upgraded canary instances can "bake" for several hours or days to verify that the upgrade did not introduce a regression. Eventually, you can upgrade the remaining instances.

## Rolling Upgrades

We recommend to automate the upgrade process with a configuration management software. It will reduce the possibility of human errors and simplify the process of managing all instances.

As of June 2016 we do not have templates for any major open-source configuration management software because our internal upgrade process is based on a proprietary software. Therefore, we invite open-source users to contribute such templates.

Any upgrade should be a rolling release i.e. usually one tablet at a time within a shard. This ensures that the remaining tablets continue serving live traffic and there is no interruption.

## Upgrading the Primary Tablet

The *primary* tablet of each shard should always be updated last in the following manner:

* verify that all *replica* tablets in the shard have been upgraded
* reparent away from the current *primary* to a *replica* tablet
* upgrade old *primary* tablet
