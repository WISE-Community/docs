---
title: Automatic Database Migration Using Flyway
layout: page
date: 2026-06-03 16:40 -0700
---

## Introduction

WISE now includes the ability to automatically apply database schema updates using [Flyway](https://flywaydb.org/). Flyway is an open-source database migration tool that ensures database schemas are consistently updated alongside application code.

With this newly added capability, when an updated version of the application is restarted, Flyway will automatically execute any necessary SQL migration scripts (located in `WISE-API/src/main/resources/db/migration`), simplifying the upgrade process for administrators.

You can learn more about how Flyway works in the [official Flyway documentation](https://documentation.red-gate.com/fd).

## Application Configuration Update

In order for existing WISE instances to use this feature, the admin will need to update the application configuration.

You must add the following property to your `application.properties` file:

```properties
spring.flyway.baseline-on-migrate=true
```

Setting `baseline-on-migrate` to `true` tells Flyway to establish a baseline for your existing database, which allows it to correctly apply new migration scripts without trying to recreate existing tables from scratch.

## Relevant commits/PR's

- API: [feat(Database): Use Flyway to manage migration #319](https://github.com/WISE-Community/WISE-API/pull/319)
