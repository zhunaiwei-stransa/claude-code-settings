# code snippet

## repository

### code 1: repository interface

```go
// apps/calendar/internal/domain/psproductdm/repository.go

package psproductdm

import (
	"context"

	"github.com/stransa-co-ltd/receipt-backend/vo/officevo"
	"github.com/stransa-co-ltd/receipt-backend/vo/patientvo"
	"github.com/stransa-co-ltd/receipt-backend/vo/psproductvo"
)

type Repository interface {
	FetchPsProducts(
		ctx context.Context,
		officeID officevo.ID,
	) ([]*PsProduct, error)
}
```

### code 2: repository impl

```go
// apps/calendar/internal/infrastructure/rdb/persistence/ps_suggestion_repository_impl.go

package persistence

var _ psproductdm.Repository = (*psProductRepository)(nil)

type psProductRepository struct {
	tm transactiondef.TransactionManager
}

func NewPsProductRepository(tm transactiondef.TransactionManager) *psProductRepository {
	return &psProductRepository{tm: tm}
}
```

### code 3: repository fetch

```go
// apps/calendar/internal/infrastructure/rdb/persistence/daily_memo_repository_impl.go

func (r *dailyMemoRepository) FetchDailyMemoByDate(
	ctx context.Context,
	officeIDVO officevo.ID,
	dateVO dailymemovo.Date,
) (*dailymemodm.DailyMemo, error) {
	db := gorm.G[*datasource.DailyMemo](r.tm.Do())

	query := db.
		Where("office_id = ?", officeIDVO.Value()).
		Where("date = ?", dateVO.Value())

	dailyMemoDS, err := query.First(ctx)
	if err != nil {
		if errors.Is(err, gorm.ErrRecordNotFound) {
			return nil, dailymemodm.ErrDailyMemoNotFound
		}

		return nil, fmt.Errorf("fetch daily memo: %w", err)
	}

	dailyMemoEntity, err := dailyMemoDS.ReconstructDailyMemoEntity()
	if err != nil {
		return nil, fmt.Errorf("reconstruct daily memo: %w", err)
	}

	return dailyMemoEntity, nil
}
```

### code 4: repository create

```go
// apps/calendar/internal/infrastructure/rdb/persistence/daily_memo_repository_impl.go

func (r *dailyMemoRepository) CreateDailyMemo(
	ctx context.Context,
	dailyMemoEntity *dailymemodm.DailyMemo,
) error {
	dailyMemoDS := datasource.NewDailyMemo(dailyMemoEntity)

	err := gorm.G[datasource.DailyMemo](r.tm.Do()).Create(ctx, dailyMemoDS)
	if err != nil {
		return fmt.Errorf("create daily memo: %w", err)
	}

	return nil
}
```

### code 5: repository update

```go
// apps/calendar/internal/infrastructure/rdb/persistence/cancel_waiting_repository_impl.go

func (r *cancelWaitingRepository) UpdateCancelWaiting(
	ctx context.Context,
	entity *cancelwaitingdm.CancelWaiting,
) error {
	cancelWaitingDS := datasource.NewCancelWaiting(entity)

	_, err := gorm.G[datasource.CancelWaiting](r.tm.Do()).
		Where("office_id = ?", cancelWaitingDS.OfficeID).
		Where("id = ?", cancelWaitingDS.ID).
		Set(clause.Assignments(map[string]any{
			"updated_at":    cancelWaitingDS.UpdatedAt,
			"date_from":     cancelWaitingDS.DateFrom,
			"date_to":       cancelWaitingDS.DateTo,
			"time_from":     cancelWaitingDS.TimeFrom,
			"time_to":       cancelWaitingDS.TimeTo,
			"wday_0":        cancelWaitingDS.Wday0,
			"wday_1":        cancelWaitingDS.Wday1,
			"wday_2":        cancelWaitingDS.Wday2,
			"wday_3":        cancelWaitingDS.Wday3,
			"wday_4":        cancelWaitingDS.Wday4,
			"wday_5":        cancelWaitingDS.Wday5,
			"wday_6":        cancelWaitingDS.Wday6,
			"required_time": cancelWaitingDS.RequiredTime,
			"menu_id":       cancelWaitingDS.MenuID,
			"menu2_id":      cancelWaitingDS.Menu2ID,
			"menu3_id":      cancelWaitingDS.Menu3ID,
			"staff_id":      cancelWaitingDS.StaffID,
			"staff2_id":     cancelWaitingDS.Staff2ID,
			"staff3_id":     cancelWaitingDS.Staff3ID,
			"memo":          cancelWaitingDS.Memo,
		})).
		Update(ctx)
	if err != nil {
		return fmt.Errorf("update cancel waiting: %w", err)
	}

	return nil
}
```

### code 6: repository delete

```go
// apps/calendar/internal/infrastructure/rdb/persistence/cancel_waiting_repository_impl.go

func (r *cancelWaitingRepository) DeleteCancelWaiting(
	ctx context.Context,
	officeID officevo.ID,
	id cancelwaitingvo.ID,
) error {
	_, err := gorm.G[datasource.CancelWaiting](r.tm.Do()).
		Where("office_id = ?", officeID.Value()).
		Where("id = ?", id.Value()).
		Delete(ctx)
	if err != nil {
		return fmt.Errorf("delete cancel waiting: %w", err)
	}

	return nil
}
```

## Points and pitfalls

1. Repository is defined in domain, and implemented in package persistence
2. Fetch, Create, Update, Delete have standardized writing style, check the code snippets
3. Fetch elements: db, query, First, NotFound, Reconstruct
4. Update(only one update method to entire update anyway, don't forget updated_at) elements: xxxDS, clause.Assignments