---
title: Updates required for the survey feature
layout: page
date: 2025-09-23 09:00 -0700
---

## Introduction

Survey feature was introduced on June 6, 2025. It allows teachers to set up unit for use without asking participants to create student accounts. Surveys are meant for shorter units that can be completed in one session and are best for anonymous data collection. Participants can only submit their responses once and cannot continue working at a later time.

## Database changes

Update runs table:

```
alter table runs add column isSurvey bit(1) not null default 0;
```

Insert new role:

```
insert into granted_authorities (authority) values ('ROLE_SURVEY_STUDENT');
```

## Relevant commits/PR's

- [WISE-Client PR](https://github.com/WISE-Community/WISE-Client/pull/2191)
- [WISE-API PR](https://github.com/WISE-Community/WISE-API/pull/306)
- [WISE-Docker-Server commit](https://github.com/WISE-Community/WISE-Docker-Server/commit/d42016278e3836755595613630fe59fb14d6c8ce)
