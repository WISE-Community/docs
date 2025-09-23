---
title: Install WISE server with Docker
layout: page
date: 2021-02-17 09:00 -0700
updated_date: 2025-09-23 09:00 -0700
---

- [Introduction](#introduction)
- [Installation](#installation)
- [Troubleshooting](#troubleshooting)
- [Properties configuration](#properties-configuration)
- [Updating with new releases](#updating-with-new-releases)
- [Application architecture](#application-architecture)
  - [API (wise-api-server container)](#api-wise-api-server-container)
  - [Client (wise-client-server container)](#client-wise-client-server-container)
  - [MySQL (wise-mysql-server container)](#mysql-wise-mysql-server-container)
  - [Redis (wise-redis-server container)](#redis-wise-redis-server-container)
  - [Nginx (wise-nginx-server container)](#nginx-wise-nginx-server-container)

# Introduction

WISE can be set up to run using Docker. We've tested on the following Linux servers:

- Ubuntu (recommended)
- Debian
- CentOS
- RedHat (Use CentOS instructions to install Docker)

Minimum recommended server specifications:

- 4GB RAM for small user base, 8GB RAM for large user base
- 20GB disk space

# Installation

1. [Install Docker Engine and Docker Compose](https://docs.docker.com/engine/install/)
2. To run Docker as a non-root user, add your user in the docker group
   - Run command to add user to docker group `$ sudo usermod -aG docker $USER`
   - Run the following command `$ newgrp docker` or logout and login again
3. Clone this project `$ git clone https://github.com/WISE-Community/WISE-Docker-Server.git`
4. Change directory `$ cd WISE-Docker-Server`
5. Run setup script and follow prompts `WISE-Docker-Server$ sh setup.sh`
6. Start services `WISE-Docker-Server$ docker compose up`
7. Open your browser and type in the address to your server. WISE should be running there with default users (be sure to change the password once you log in)
   - admin/pass
   - preview/wise

# Troubleshooting

1. If you are having trouble loading the WISE homepage, it may be because your server does not have HTTPS enabled, but your browser is trying to access the HTTPS URL. To get past this, remove the "s" from "https" in the URL.

# Properties configuration

You can edit the `/WISE-Docker-Server/wise-api-server/application.properties` file to enable certain features on the server.

Setting this value will enable Google Analytics. This keeps track of data on your users such as where they are from and what type of device they are using. You will need to generate your own Google Analytics id on the [Google Analytics](https://marketingplatform.google.com/about/analytics/) site.

```
google_analytics_id=
```

Setting these values will enable reCAPTCHA on the server. This helps protect against bots that try to interact with the server. You can generate these keys on the [reCAPTCHA](https://developers.google.com/recaptcha/) site.

```
recaptcha_public_key=
recaptcha_private_key=
```

Setting these values will enable the server to send emails. Email is used to send account creation confirmation emails, change passwords, and send support tickets.

```
spring.mail.username=
spring.mail.password=
```

These email addresses will receive emails from the server when certain events occur.

```
contact_email=
project_setup=
handle_exception=
uber_admin=
portalemailaddress=
```

Setting these values will enable users to sign in with a Google account. It also allows users to integrate WISE with Google Classroom. You will need to generate your own Google client id and client secret by following the directions [here](https://developers.google.com/identity/sign-in/web/sign-in).

```
google.clientId=
google.clientSecret=
google.redirectUri=
```

Setting these values will enable users to sign in with a Microsoft account. You will need to generate your own Microsoft values using the instructions [here](https://learn.microsoft.com/en-us/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory).

```
microsoft.accessTokenUri=
microsoft.clientId=
microsoft.clientSecret=
microsoft.issuer=
microsoft.jwkUrl=
microsoft.userAuthorizationUri=
microsoft.redirectUri=
```

Setting these values will enable the Discourse forum where users can post messages. You will need to start up your own Discourse server and obtain the values from it using the instructions [here](https://github.com/discourse/discourse/blob/main/docs/INSTALL.md).

```
discourse_url=
discourse_sso_secret_key=
```

Setting these values will enable the speech to text feature that allows students to speak into an Open Response item and have their words automatically converted to text. You will need to set up the transcribe service on AWS using the instructions [here](https://docs.aws.amazon.com/transcribe/latest/dg/streaming-setting-up.html).

```
speech-to-text.aws.region=
speech-to-text.aws.identity-pool-id=
```

Setting this value will enable the AI Chat item that lets students chat with ChatGPT. You will need to generate your own Open AI API key using the instructions [here](https://help.openai.com/en/articles/4936850-where-do-i-find-my-openai-api-key).

```
OPENAI_API_KEY=
```

# Updating with new releases

1. Make necessary backups (database, curriculum, configuration files, etc.)
2. Stop Docker containers `WISE-Docker-Server$ docker compose down`
3. Get updates to Docker containers `WISE-Docker-Server$ docker compose pull`
4. Get updates to Docker-Docker-Server project `WISE-Docker-Server$ git pull`
5. Make any necessary updates to configuration files and databases
6. Start Docker containers `WISE-Docker-Server$ docker compose up`

# Application architecture

## API (wise-api-server container)

This contains the API code that handles requests from the client. Whenever the client makes a request to the API, the API performs processing and sends back a response. The API saves user data and unit data to the MySQL database. The API also saves the curriculum and student uploads to the file system.

## Client (wise-client-server container)

This contains the client code that gets run on the browser.

## MySQL (wise-mysql-server container)

This contains the database where the user data and unit data is stored.

## Redis (wise-redis-server container)

This contains the session database where the user session data is stored.

## Nginx (wise-nginx-server container)

This contains an http server that reads the curriculum content and student uploads from the file system and sends it to the client whenever client requests it.
