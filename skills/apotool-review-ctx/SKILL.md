---
name: apotool-review-ctx
description: 'In short: A context provider in skill that must understand user input intention well; Triggers: "/apotool-review-ctx"'
---

# Role

A context provider in skill that must understand user input intention well


## Renewal special

### soft link

PHP main code: link-apotool_master -> ../apotool_master
PHP calendar app code: link-apotool_calendar -> ../apotool_calendar
review output dir: link-renewal-review -> ../renewal-review
Frontend dir: link-apotool-web -> ../apotool-web

created by

```
ln -s ../apotool_master ./link-apotool_master
ln -s ../apotool_calendar ./link-apotool_calendar
ln -s ../renewal-review ./link-renewal-review
ln -s ../apotool-web link-apotool-web
```

### codebase

- git@github.com:stransa-co-ltd/stransa-backend.git

### PreCheck soft link

- Make sure soft link in repo root exist, or that reject this request and remind user to add the link

# Target

- Obey user instruction, this skill file only provide context

## rules

- I have final power to review your job, so I need filepath of code snippet no matter for what repository

## job special

### critical-path-apis-research

At first, we are in human-AI collaboration review mode.
In this mode, each api has one independent persistence file, but can persist what you think which can be shared in context.md.
I only describe what you do on your every session.
- API must-have message
1. API independent file
2. reading old source code from apotool_master and apotool_calendar
3. reading new source code from codebase

- for each api, if you are lack of must-have message, you must build it or read it
- after you fully load must-have message, you should collaborate with my user prompt to review codebase code, we focus on these aspects
1. potential bugs: abandon critical logic from old code(sometimes difference is new feature, but I think you can easily distinguish), new Go impl bug, etc.
2. abstraction insufficient: too long function, capsulation of business logic where it should have, etc.
3. over or bad abstraction: God class, bad interface, class that must first read it then can  understand what it do
4. jst/utc problem: as we have a gormtype pkg to convert right time/date into mysql, in principle the memory time and date should be jst. Time can cause very hidden bug, so take serious and think deep into it.

- at last, you have become a pro of this api, so you need to write and maintain a easy understanding explanation for tech leader or developer who never develop this api to master the information of how this api is implemented

## context

### renewal-review

- we only focus on backend of apps/apotool, apps/receipt is not in my business

## persistence

- you must at least persist one file for one big user intension, stored in renewal-review/{job}/{task}
- you should maintain only one context file for one job, stored in renewal-review/{job}/context.md

# Constrains

- Human reading friendly output in markdown format
- Language: English!