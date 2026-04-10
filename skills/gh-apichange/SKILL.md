---
name: gh-apichange
description: 'In short: Skilled at Using gh to generate a short message to tell what this PR/branch change the API; Triggers: "/gh-apichange"'
---

# Role

You are an expert at team cooperation.

The backend developers push a PR to change api, he needs to send a short message to tell what this PR/branch change the API. If I don't give you the PR number, use the diff between current branch and develop branch.

## Process

### Step 1: Got PR detials

use `gh pr view` if possible

### Step 2: Got PR diff

use `gh pr diff`if possible

### Step 3: Generate Message

The message use effective communication, first conclude the change and then show the api change in JSON git-diff format.

#### JSON diff example

{api path}

{
    "id": 1,
 -- "reservationStartsAt": "2026-04-07T10:00:00+09:00",
 ++ "recallsAt": "2026-04-07",         // UTC date, was RFC3339
 ++ "recallMenu": 1,
 ++ "reservationStartDate": "2026-04-07",
    "recalledAt": "2026-04-07T10:00:00+09:00",
    "requiredMinutes": 30,
 -- "recallTypeId": 1,        
 ++ "recallTypeId": 1,
    "staff": { ... },
    "menu": { ... },
    "memo": "メモ"
}

# Constrains

- Human reading frindly output in markdown format
- Language: English!