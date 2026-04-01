# code snippet

## datasource

### code 1

```go
package datasource

import (
	"fmt"
	"time"

	"gorm.io/gorm"

	"github.com/stransa-co-ltd/receipt-backend/apps/calendar/internal/domain/psproductdm"
)

const TableNamePsSuggestion = "ps_suggestions"

// PsSuggestion 物販商品提案
type PsSuggestion struct {
	ID          uint64         `gorm:"column:id"`
	CreatedAt   time.Time      `gorm:"column:created_at"`
	UpdatedAt   time.Time      `gorm:"column:updated_at"`
	DeletedAt   gorm.DeletedAt `gorm:"column:deleted_at"`
	OfficeID    uint64         `gorm:"column:office_id"`
	PatientID   uint64         `gorm:"column:patient_id"`
	SuggestedAt time.Time      `gorm:"column:suggested_at"`
	Conditions  *string        `gorm:"column:conditions"`
}

// TableName PsSuggestion's table name
func (*PsSuggestion) TableName() string {
	return TableNamePsSuggestion
}

func NewPsSuggestion(entity *psproductdm.PsSuggestion) *PsSuggestion {
	return &PsSuggestion{
		ID:          entity.ID().Value(),
		CreatedAt:   entity.CreatedAt().Value(),
		UpdatedAt:   entity.UpdatedAt().Value(),
		OfficeID:    entity.OfficeID().Value(),
		PatientID:   entity.PatientID().Value(),
		SuggestedAt: entity.SuggestedAt().Value(),
		Conditions:  entity.Conditions().NullableValue(),
	}
}

func (m *PsSuggestion) ReconstructPsSuggestionEntity() (*psproductdm.PsSuggestion, error) {
	entity, err := psproductdm.ReconstructPsSuggestion(
		m.ID,
		m.OfficeID,
		m.PatientID,
		m.SuggestedAt,
		m.Conditions,
		m.CreatedAt,
		m.UpdatedAt,
	)
	if err != nil {
		return nil, fmt.Errorf("reconstruct ps suggestion: %w", err)
	}

	return entity, nil
}
```

### code 2: utils

```go
func NullableTimeFromDeletedAt(deletedAt gorm.DeletedAt) *time.Time

func DeletedAtFromNullableTime(t *time.Time) gorm.DeletedAt
```

### Points and pitfalls:
1. datasource is the database model
2. New$Datasource create a datasource from domain entity
3. func (m *Datasource) Reconstruct${Datasource}Entity() use domain Recontruct to create entity
4. When you want to new datasource from enity, you can use .Value() or .NullableValue(), no need to get address

## Constrains

- You should only never modify type DataSource struct def unless you have special good reason