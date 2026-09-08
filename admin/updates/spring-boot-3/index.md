---
title: Updates required for SpringBoot 3 upgrade
layout: page
date: 2026-09-08 10:00 -0700
---

## Introduction

WISE has been upgraded from SpringBoot 2.7 to SpringBoot 3.2. As part of this upgrade, dependencies and library packages were modernized, and the OAuth authentication flow was rewritten to follow the modern Spring Boot OAuth2 client implementation.

## Application Configuration Updates

If your WISE instance allows authentication with Google, administrators will need to update `application.properties` and add an authorized redirect URI in Google Cloud Console.

### 1. `application.properties` Changes (OAuth Google and Microsoft)

The property names for Google and Microsoft OAuth configurations have changed. If you are using Google or Microsoft for authentication, you will need to update `application.properties` and add an authorized redirect URI in Google Cloud Console or Microsoft Azure AD.

#### OAuth2 Client Configuration for Google

```properties
spring.security.oauth2.client.registration.google.client-id=
spring.security.oauth2.client.registration.google.client-secret=
spring.security.oauth2.client.registration.google.scope=openid,email,profile
```

Fill in your Google client ID and client secret in the respective fields.

# OAuth2 Client Configuration for Microsoft

```properties
spring.security.oauth2.client.registration.microsoft.client-id=
spring.security.oauth2.client.registration.microsoft.client-secret=
spring.security.oauth2.client.registration.microsoft.scope=openid,email,profile
spring.security.oauth2.client.registration.microsoft.client-name=Microsoft
spring.security.oauth2.client.registration.microsoft.authorization-grant-type=authorization_code
spring.security.oauth2.client.registration.microsoft.redirect-uri={baseUrl}/login/oauth2/code/{registrationId}
spring.security.oauth2.client.provider.microsoft.authorization-uri=https://login.microsoftonline.com/common/oauth2/v2.0/authorize
spring.security.oauth2.client.provider.microsoft.token-uri=https://login.microsoftonline.com/common/oauth2/v2.0/token
spring.security.oauth2.client.provider.microsoft.jwk-set-uri=https://login.microsoftonline.com/common/discovery/v2.0/keys
spring.security.oauth2.client.provider.microsoft.user-name-attribute=sub
```

### 2. Google Cloud Console Redirect URI

You will also need to add a new authorized redirect URI in the [Google Cloud Console](https://console.cloud.google.com/) for your OAuth 2.0 Client ID:

```
/login/oauth2/code/google
```

For example:

- For production: `https://<your-domain>/login/oauth2/code/google`
- For local development: `http://localhost/login/oauth2/code/google`

## Relevant commits/PR's

- [WISE-API PR](https://github.com/WISE-Community/WISE-API/pull/311)
- [WISE-Client PR](https://github.com/WISE-Community/WISE-Client/pull/2251)
- [WISE-Docker-Dev PR](https://github.com/WISE-Community/WISE-Docker-Dev/pull/19)
