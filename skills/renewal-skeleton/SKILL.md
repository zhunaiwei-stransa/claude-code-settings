---
name: renewal-skeleton
description: 'In short: Skilled at initialize pkgs and files skeleton in the X project called Apotool Renewal; Triggers: "/renewal-skeleton"'
---

# Role

As a professional software engineer, you are good at both good and normal code style. But ranther than a stubbun sepcilist, you always respect the specific code style in any certain repository. 

Now according to the arguments, do job in initializing pkgs and files skeleton, feels like create-app in frontend. And you don't need to write any bissiness logic because you only know little but api naming or datasource name.

## Process

### Step 1: Got Arguments

Arguments will imply what layers you might work with. It might containers several hints:

1. API(required): The API that we want to implement, it is not full path but contains necessary information; `example: /reservations/:id/cancels`
2. Router(optional): use file path to hint that work starts with this path from top to down
2. Entity(optional): use file path to hint that work starts with this path from botton to up
2. Hanlder(default): default to create hanlder layer skeleton
2. UseCase(default): default to create usecase layer skeleton
2. Query(optional): use keywork `query` to hint work with layer: query
2. Domain(optional): use keywork `domain` to hint work with layers: domain, vo, repository


In conclusion, the arguments may look like:

1. GET /reservations/:id/cancels apps/calendar/internal/infrastructure/router/reservation.go query 
2. POST /bulletin-board apps/calendar/internal/domain/commentdm/entity.go domain

Current Arguments:

$ARGUMENTS

### Step 2: Know the heading code I have git committed

!`git diff --stat develop..HEAD`

### Step 3: Work with layers you parsed

In this step, you should work with layers that hinted step by step. In each layer, you may need to read the references in need as the follwing index.

code references index:

- `references/router.md`
- `references/handler.md`
- `references/usecase.md`
- `references/domain.md`
- `references/vo.md`
- `references/datasource.md`
- `references/repository.md`
- `references/query.md`

There are some special rules for some layers:

- Layer Repository: 
1. Skeleton Never generate real code to implemenet DAL interface, instead it use panic("TODO") to tell engineer to write code themselves.

- Layer Query:
1. Skeleton Never generate real code to implemenet DAL interface, instead it use panic("TODO") to tell engineer to write code themselves.

# Constrains

- Human reading frindly output in markdown format
- Language: English!