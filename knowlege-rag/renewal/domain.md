# code snippet

## domain

### code 1: entity, getter, changer

```go
// apps/calendar/internal/domain/psproductdm/entity_suggestion.go

package psproductdm

import (
	"github.com/stransa-co-ltd/receipt-backend/vo/officevo"
	"github.com/stransa-co-ltd/receipt-backend/vo/patientvo"
	"github.com/stransa-co-ltd/receipt-backend/vo/psproductvo"
	"github.com/stransa-co-ltd/receipt-backend/vo/sharedvo"
)

type PsSuggestion struct {
	id          psproductvo.SuggestionID
	officeID    officevo.ID
	patientID   patientvo.ID
	suggestedAt psproductvo.SuggestedAt
	conditions  *psproductvo.Conditions
	createdAt   sharedvo.AuditTime
	updatedAt   sharedvo.AuditTime
}

func newPsSuggestion(
	id psproductvo.SuggestionID,
	officeID officevo.ID,
	patientID patientvo.ID,
	suggestedAt psproductvo.SuggestedAt,
	conditions *psproductvo.Conditions,
	createdAt sharedvo.AuditTime,
	updatedAt sharedvo.AuditTime,
) *PsSuggestion {
	return &PsSuggestion{
		id:          id,
		officeID:    officeID,
		patientID:   patientID,
		suggestedAt: suggestedAt,
		conditions:  conditions,
		createdAt:   createdAt,
		updatedAt:   updatedAt,
	}
}

func (s *PsSuggestion) ID() psproductvo.SuggestionID {
	return s.id
}

func (s *PsSuggestion) OfficeID() officevo.ID {
	return s.officeID
}

func (s *PsSuggestion) PatientID() patientvo.ID {
	return s.patientID
}

func (s *PsSuggestion) SuggestedAt() psproductvo.SuggestedAt {
	return s.suggestedAt
}

func (s *PsSuggestion) Conditions() *psproductvo.Conditions {
	return s.conditions
}

func (s *PsSuggestion) CreatedAt() sharedvo.AuditTime {
	return s.createdAt
}

func (s *PsSuggestion) UpdatedAt() sharedvo.AuditTime {
	return s.updatedAt
}

func (s *PsSuggestion) ChangeConditions(conditions *psproductvo.Conditions) {
	s.conditions = conditions
}
```

### code 2: ReconstructXXX

```go
// apps/calendar/internal/domain/psproductdm/reconstruct.go

func ReconstructPsSuggestion(
	id uint64,
	officeID uint64,
	patientID uint64,
	suggestedAt time.Time,
	conditions *string,
	createdAt time.Time,
	updatedAt time.Time,
) (*PsSuggestion, error) {
	idVO, err := psproductvo.NewSuggestionID(id)
	if err != nil {
		return nil, fmt.Errorf("new suggestion id: %w", err)
	}

	officeIDVO, err := officevo.NewID(officeID)
	if err != nil {
		return nil, fmt.Errorf("new office id: %w", err)
	}

	patientIDVO, err := patientvo.NewID(patientID)
	if err != nil {
		return nil, fmt.Errorf("new patient id: %w", err)
	}

	suggestedAtVO, err := psproductvo.NewSuggestedAt(suggestedAt)
	if err != nil {
		return nil, fmt.Errorf("new suggested at: %w", err)
	}

	conditionsVO, err := psproductvo.NewNullableConditions(conditions)
	if err != nil {
		return nil, fmt.Errorf("new conditions: %w", err)
	}

	createdAtVO, err := sharedvo.NewAuditTime(createdAt)
	if err != nil {
		return nil, fmt.Errorf("new created at: %w", err)
	}

	updatedAtVO, err := sharedvo.NewAuditTime(updatedAt)
	if err != nil {
		return nil, fmt.Errorf("new updated at: %w", err)
	}

	return newPsSuggestion(
		idVO,
		officeIDVO,
		patientIDVO,
		suggestedAtVO,
		conditionsVO,
		createdAtVO,
		updatedAtVO,
	), nil
}
```


### code 3: errors

```go
// apps/calendar/internal/domain/psproductdm/errors.go

package psproductdm

import "errors"

var (
	ErrPsSuggestionNotFound = errors.New("ps suggestion not found")
)
```


### code 4: GenXXXForCreate(optional)

```go
// apps/calendar/internal/domain/psproductdm/factory.go

func GenPsSuggestionForCreate(
	officeID officevo.ID,
	patientID patientvo.ID,
	suggestedAt psproductvo.SuggestedAt,
	conditions *psproductvo.Conditions,
) *PsSuggestion {
	now := time.Now().UTC()
	createdAtVO, _ := sharedvo.NewAuditTime(now)

	return newPsSuggestion(
		0,
		officeID,
		patientID,
		suggestedAt,
		conditions,
		createdAtVO,
		createdAtVO,
	)
}
```

## Points and pitfalls

1. one entity one file
2. new$Entityname is the only way to initial a entity, params are vo
3. getter's name is the Captitaled field name
4. Reconstruct$Entityname is the way to use underlying type instead of vo to call new$Entityname
5. Create API use Gen$EntitynameForCreate to create entity in memory
6. In entity, a filed is a value or pointer is strongly consistent with the datasource filed