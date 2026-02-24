---
name: refactor-move
description: 'In short: Skilled at moving code in the X project called Apotool Renewal; Triggers: "/refactor-move"'
---

# Role

As a professional software engineer, you are good at both good and normal code style. But ranther than a stubbun sepcilist, you always respect the specific code style in any certain repository. So you are ask to move code from Repository apotool-backend into Repository receipt-backend.

For convenience, I make a soft link from apotool-backend to "@link-apotool-backend". When I give you a file path of apotool-backend like `infra/rdb/persistence/queryadapter/cancel_waiting_query.go`, you always know it is really in `./link-apotool-backend/infra/rdb/persistence/queryadapter/cancel_waiting_query.go`

## Process

### Step 1: Focus on target

Endpoint and original file path of apotool-backend:

"$ARGUMENTS"

### Step 2: Move Code level by level: Router and Handler

- The likely router location of apotool-backend is `apps/calendar/internal/router/router.go`;
- The likely router location of receipt-backend is by files in domains such as `apps/calendar/internal/infrastructure/router/dailymemo.go`;

- The likely handler location of apotool-backend is by files in domains such as `apps/calendar/internal/handler/daily_memo_handler.go`;
- The likely handler location of receipt-backend is by files in domains such as `apps/calendar/internal/handler/dailymemohandler/fetch_daily_memo_handler.go`;

### Step 3: Move Code level by level: UseCase

- The likely usecase location of apotool-backend is by dirs in domains such as `apps/calendar/internal/usecase/dailymemousecase/`;
- The likely usecase location of receipt-backend is by files and by class in domains such as `apps/calendar/internal/usecase/dailymemousecase/fetch_daily_memo_use_case.go`;

### Step 4: Move Code level by level: Domain(Entity and Repository and Error) and VO(Valueobject)

- The likely domain location of apotool-backend is by dirs such as `domain/dailymemodm/dailymemo/`, including vo;
- The likely domain location of receipt-backend is by dirs such as `apps/calendar/internal/domain/dailymemodm/`;
- The likely vo location of receipt-backend is by dirs such as `vo/dailymemovo/`;

### Step 5: Move Code level by level: RepositoryImpl or Query

- The likely repostory-impl location of apotool-backend is by files `infra/rdb/persistence/adapter/daily_memo.go`;
- The likely repostory-impl location of receipt-backend is by files `apps/calendar/internal/infrastructure/rdb/persistence/daily_memo_repository_impl.go`;

- The likely query-def location of apotool-backend is by dirs such as `query/shiftquery/shift_query.go`;
- The likely query-def location of receipt-backend is by dirs such as `apps/calendar/internal/query/shiftquery/shift_query.go`;

- The likely query-impl location of apotool-backend is by files such as `infra/rdb/persistence/queryadapter/shift_query.go`;
- The likely query-impl location of receipt-backend is by files such as `apps/calendar/internal/infrastructure/rdb/persistence/queryimpl/shift_query_impl.go`;

## Ref

If you are not sure about code styling, you have 2 ways to confirm:

1. Read Skill references/receipt-backend.md
2. If still unsure, read local code directly

## Pitfalls

### Var Naming

- In hanler level, be careful of the full naming of vars of transactionManager, xxxUseCase, xxxHandler
- Use `echoapi.BindAndValidate(c, in)` instead of `c.Bind(in)`
- Entity-type var is named like xxxEntity, xxxEntities
- DataSource-type var is named like xxxDS, xxxDSs
- Update API change Entity in memory, use .ChangeXXX(vo) one by one
- Use ErrXXXNotFound that sholud be defined in domain level

### Profound logging

- Use log wrapping like `fmt.Errorf("fetch calendar groups: %w", err)` in every error handling of bisuness logic, even though a vo creation will write like this: 

```go
officeIDVO, err := officevo.NewID(in.OfficeID)
if err != nil {
  return nil, httperror.BadRequest(
    config.ErrCodeBadRequest,
    fmt.Errorf("new office id: %w", err),
  )
}
```

### DDD in this case

- In this case, Repository and Query are seperate concept; Repository belongs to domain, but Query bypasses the domain

# Constrains

- Always do one API in an entire chat
- Feel good to adapt into the level of other engineers who are in this project, so don't need to make the code best but suitable
- No need to think about testing
- Be careful of the naming of files or directory of each level
- Language: English!