---
title: Student work saving scenarios
layout: page
date: 2025-10-06 09:00 -0700
---

Here are possible scenarios of when the student's work is saved:

- Student explicitly saves work by hitting the "save" or "submit" button...
  - on an item => only saves/submits the item
  - on a step => saves/submits any unsaved items in the step and assigns same save timestamp to all items
- WISE automatically saves work
  - WISE checks for any unsaved items in the step (student chose an answer or typed in a response but has not saved/submitted it). If found, WISE will save them all and assign them the same save timestamp
    - Every 60 seconds
    - When the student moves to the next step or exits the unit
