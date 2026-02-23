---
title: Updates required for the AI Assistant feature
layout: page
date: 2026-01-13 09:00 -0700
---

## Introduction

AI Assistant feature was introduced on January 13, 2026. It is a persistent chatbot tool designed to help you navigate your curriculum, brainstorm ideas, and ask questions directly within the platform. You can read more about this feature [here](../../../user/feature/ai-assistant/).

This feature currently requires an AWSBedrock account to facilitate talking to an LLM. In the future, we will look into supporting OpenAI with API key access.

## Database changes

Update runs table:

```
create table chatbot_chats (
    id bigint not null auto_increment,
    runId bigint not null,
    workgroupId bigint not null,
    title varchar(255),
    createdAt datetime not null,
    lastUpdated datetime not null,
    isDeleted bit not null default 0,
    index chatbotChatsRunIdIndex (runId),
    index chatbotChatsWorkgroupIdIndex (workgroupId),
    constraint chatbotChatsRunIdFK foreign key (runId) references runs (id),
    constraint chatbotChatsWorkgroupIdFK foreign key (workgroupId) references workgroups (id),
    primary key (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

create table chatbot_messages (
    id bigint not null auto_increment,
    chatId bigint not null,
    role varchar(20) not null,
    content text not null,
    timestamp datetime,
    nodeId varchar(30) not null,
    index chatbotMessagesChatIdIndex (chatId),
    constraint chatbotMessagesChatIdFK foreign key (chatId) references chatbot_chats (id) on delete cascade,
    primary key (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

## Configuration changes

Update `application.properties` with AWS Bedrock API key and runtime endpoint:

```
aws.bedrock.api.key=(ex: AKIAIOSFODNN7EXAMPLE)
aws.bedrock.runtime.endpoint=(ex: https://bedrock-runtime.us-east-1.amazonaws.com)
```

## Relevant commits/PR's

- [WISE-Client PR](https://github.com/WISE-Community/WISE-Client/pull/2252)
- [WISE-API PR](https://github.com/WISE-Community/WISE-API/pull/313)
