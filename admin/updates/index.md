---
title: Application Updates
layout: page
date: 2026-09-08 10:00 -0700
---

## Introduction

This page contains updates to the application that require updates to the configuration files, database or both. Be sure to check the list of updates below to make the necessary updates to the `application.properties` file. Database migrations occur automatically when an updated application is restarted using the Flyway migration tool (the sql files are located in `WISE-API/src/main/resources/db/migration`).

## List of updates

- [SpringBoot 3 upgrade (September 2026)](spring-boot-3)
- [Automatic database migration using Flyway (June 2026)](automatic-db-migration)
- [AI Assistant feature (January 2026)](ai-assistant)
- [Survey feature (June 2025)](survey)
