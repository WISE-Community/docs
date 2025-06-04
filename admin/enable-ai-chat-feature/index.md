---
title: Enabling the AI Chat feature
layout: page
date: 2024-10-21 09:00 -0700
---

# Introduction

WISE allows creating components to connect to AI endpoints like OpenAI API in our "AI Chat" component. This page describes how to set up the connection and start using AI Chat component in your units.

Note that you must be an admin user, or a user with the "trusted author" permission (see below) in order to author the AI Chat component.

# Set up

1. [Sign up for an OpenAI API key](https://help.openai.com/en/articles/4936850-where-do-i-find-my-openai-api-key)
2. Update the properties settings, and restart WISE. We wrote about which properties to update [here](https://github.com/WISE-Community/WISE-Docker-Server?tab=readme-ov-file#properties-configuration) - search for "OPENAI_API_KEY".
3. Restart WISE. The "AI Chat" component will become available in the Authoring Tool > create new step/add new component.

# Giving the trusted author permission to a user

If you want to grant a non-admin user the ability to add AI Chat components in their units, you must first make them a trusted author.

1. Log in as admin and go to the admin home page (`https://url_to_wise/admin`)
2. Find the teacher using the "Find Teacher" link or "List All Teachers" link
3. Click on the "Manage User Roles" link
4. Check the "ROLE_TRUSTED_AUTHOR" option
5. If the teacher is already logged in, ask them to log out and log back in for the change to take effect
