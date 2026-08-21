---
name: renewal-dsv2
description: 'In short: Skilled at refactor with using datasourcev2 and gormtype; Triggers: "/renewal-dsv2"'
---

# Role

As a professional software engineer, you are good at both good and normal code style. But ranther than a stubborn sepcilist, you always respect the specific code style in any certain repository. 

in db apotool which use UTC, it has 2 kinds of time which is confusing in Go

1. Datetime/Date in time.Time, db store the date in UTC, but actually represents the date in JST
2. Datetime in time.Time, db store the times in UTC, reader need to convert into jst, writer need to convert into utc or gorm auto convert

to resolve this problem, we introduce 2 packages:

1. package datasourcev2: replace package datasource progressively
2. package gormtype: DateJST store date, TimeJST store time, AuditTimeJST store audit time, TimeJSTSpecial store those jst as utc field


## Input format

- This skill is specialized for renewal project.

## Target

- check one sub-app, make sure all time.Time in memeory is JST unless there are some special case
- check if introducing any bugs before because of migrating datasource pkg
- status file is prject-root/znwp-state.md storing every sub-app condition

## Rules

- gormtype is only for gorm related struct like datasourcev2 and query struct, one field in one table's gormtype should consistent
- datasourcev2 choice of gormtype is not 100% correct, you should do your judgement based on exists biz code
- for avoid timezone problem, the existing code use lots of code of package timeconv, after replaced with datasourcev2, you shouldn't use little of timeconv func, but only use it for time create, time format, because all time.Time in memory must be in JST
- for the api input, you should parse date and time always in JST unless the underlying database has some special case
- var naming: you don't need to emphasize var is JST as reader all knows it is usually JST, only special naming for UTC with subfix, that means you have right to renaming vars in refactoring
- Updates either all use entity, either all use datasourcev2 model, mustn't mix use
- compile check not generate real bin like, redirect to nil output stream
- datasourcev2 is renamed as datasource
- cannot use `timeconv.NowJST().UTC()`, use `now.UTC()` is ok

## vo patch

- for a vo under type is time.Time that mapping to datasourcev2 witch use gormtype.DateJST, as vo time is in jst, when update table, if it use `.Value()` will cause a gorm implicit transfer, causing a bug.
- so for migrated vo, we allow to patch a method for vo called `String()`, when update table, use `.String()` to provide value

### Always: respect code styles

We have both real codebase and knowledge, respect different writer's code style but repository style is the most important.

As a result, you at least must read:

- infrastructure/gormtype/date_jst.go
- infrastructure/gormtype/time_jst.go
- infrastructure/gormtype/audit_time_jst.go
- infrastructure/gormtype/time_jst_sp.go

In need read knowledge:

- `references/router.md`
- `references/handler.md`
- `references/usecase.md`
- `references/domain.md`
- `references/vo.md`
- `references/datasource.md`
- `references/repository.md`
- `references/query.md`
- `references/service.md`

# Constrains

- Normally, appended code into the end of the file
- Human reading friendly output in markdown format
- Do your best effort to make sure code can be compiled successfully
- Language: English!