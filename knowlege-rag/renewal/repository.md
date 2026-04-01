# code snippet

## repository

### code 1: repository interface

```go
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
func (r *cancelWaitingRepository) UpdateCancelWaiting(
	ctx context.Context,
	entity *cancelwaitingdm.CancelWaiting,
) error {
	cancelWaitingDS := datasource.NewCancelWaiting(entity)

	_, err := gorm.G[datasource.CancelWaiting](r.tm.Do()).
		Where("office_id = ?", cancelWaitingDS.OfficeID).
		Where("id = ?", cancelWaitingDS.ID).
		Set(clause.Assignments(map[string]any{
			"updated_at":    time.Now().UTC(),
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
func (r *cancelWaitingRepository) HardDeleteCancelWaiting(
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

```go
func (r *fileRepository) SoftDeleteMediaByIDs(
	ctx context.Context,
	officeIDVO officevo.ID,
	mediaIDVOs []filevo.MediaID,
) (int, error) {
	if len(mediaIDVOs) == 0 {
		return 0, nil
	}

	rowsAffected, err := gorm.G[*datasource.Media](r.tm.Do()).
		Where("id IN ?", mediaIDVOs).
		Where("office_id = ?", officeIDVO.Value()).
		Where("deleted_at IS NULL").
		Set(
			clause.Assignments(map[string]any{
				"updated_at": gorm.Expr("updated_at"),
				"deleted_at": sharedvo.NewAuditTimeNow().Value(),
			}),
		).
		Update(ctx)
	if err != nil {
		return 0, fmt.Errorf("soft delete media: %w", err)
	}

	return rowsAffected, nil
}
```

### code 3: repository fetch withLock

```go
func (k *karteRepository) FetchLockedPatientLockByPatientID(
	ctx context.Context,
	officeIDVO officevo.ID,
	patientIDVO patientvo.ID,
	withLock bool,
) (*kartedm.PatientLock, error) {
	db := gorm.G[*datasource.KartePatientLock](k.tm.Do(), withLockClause(withLock)...)
// ...
}

func withLockClause(withLock bool) []clause.Expression {
	if withLock {
		return []clause.Expression{clause.Locking{Strength: "UPDATE"}}
	}

	return nil
}
```

## Points and pitfalls

1. Repository is defined in domain, and implemented in package persistence
2. Fetch(not Get or List), Create, Update, Delete have standardized writing style, check the code snippets
3. Fetch elements: db, query, First, NotFound, Reconstruct
4. Update(only one update method to entire update anyway, don't forget updated_at) elements: xxxDS, clause.Assignments
5. updated_at use live generate time.Now, not the memory one
6. Delete method is explicitly naming as SoftDelete using Update or HardDelete using Delete. SoftDelete use `"updated_at":  gorm.Expr("updated_at"),` to avoid update updated_at.

## Constrains

1. For most tables, especially small and middle table, only have one update method to update from a entire entity
2. When generate Update method, only generate specific fields update for the api you are working. That means you don't need to generate Update method when the api do not need update method.
3. Save method is always forbbidden, instead use Fetch-Change-Update-Or-Create logic in UseCase layer.
4. If want to use `clause.Locking{Strength: "UPDATE"}`, add withLock bool param in Fetch method, and update all references. You cannot create any new method just for a select for update