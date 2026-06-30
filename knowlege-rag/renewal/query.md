# code snippet

## query

### code 1: query interface

```go
package pssuggestionquery

import (
	"context"
	"time"

	"github.com/stransa-co-ltd/receipt-backend/apps/calendar/internal/infrastructure/rdb/persistence/datasource"
)

type Query interface {
	FetchPsSuggestion(
		ctx context.Context,
		officeID uint64,
		patientID uint64,
		date time.Time,
	) (*datasource.PsSuggestion, error)
	FetchPsSuggestionLines(
		ctx context.Context,
		officeID uint64,
		psSuggestionID uint64,
	) ([]datasource.PsSuggestionLine, error)
	FetchPsSuggestionLineJoinProducts(
		ctx context.Context,
		officeID uint64,
		patientID uint64,
		date time.Time,
	) (*PrintPsSuggestionLineAggr, error)
}
```

### code 2: query impl

```go
package queryimpl

var _ pssuggestionquery.Query = (*PsSuggestionQuery)(nil)

type PsSuggestionQuery struct {
	tm transactiondef.TransactionManager

	dtbTaxRuleQuery dtbtaxrulequery.Query
	regiConfigQuery regiconfigquery.Query
}

func NewPsSuggestionQuery(
	tm transactiondef.TransactionManager,
	dtbTaxRuleQuery dtbtaxrulequery.Query,
	regiConfigQuery regiconfigquery.Query,
) pssuggestionquery.Query {
	return &PsSuggestionQuery{
		tm:              tm,
		dtbTaxRuleQuery: dtbTaxRuleQuery,
		regiConfigQuery: regiConfigQuery,
	}
}
```

### code 2: query method

```go
func (a *PatientQuery) getPatientAggrForEditProfile(
	ctx context.Context,
	officeID uint64,
	patientID uint64,
) (*patientquery.PatientAggrForEditProfile, error) {
	var aggr patientquery.PatientAggrForEditProfile

	res := a.tm.Do().WithContext(ctx).Model(&datasource.Patient{}).
		Select(gormtag.ReflectSelect(aggr)).
		Joins(`
			LEFT JOIN md_patients ON
			patients.id = md_patients.patient_id AND
			patients.office_id = md_patients.office_id
		`).
		Joins(`
			LEFT JOIN line_data ON
			patients.id = line_data.patient_id AND
			patients.office_id = line_data.office_id
		`).
		Joins(`
			LEFT JOIN line_patients ON
			patients.id = line_patients.patient_id AND
			patients.office_id = line_patients.office_id
		`).
		Where("patients.office_id = ?", officeID).
		Where("patients.id = ?", patientID).
		Take(&aggr)
	if err := res.Error; err != nil {
		if errors.Is(err, gorm.ErrRecordNotFound) {
			return nil, patientdm.ErrPatientNotFound
		}

		return nil, fmt.Errorf("fetch patient: %w", err)
	}

	return &aggr, nil
}
```

## Points and pitfalls

1. Query is defined in independent pkg, and implemented in package queryimpl
2. Repository and Query are seperate concept; Repository belongs to domain, but Query bypasses the domain
3. Query impl may use join, as office_id is an important id to avoid data misquery, so we must write redundant code to check tableMain.office_id = tableSub.office_id. But not every table has column office_id, you can check this in datasource pkg
4. For query pkg, xxx_item.go is usually only one
5. Join use beautiful format of anti-quote like Joins(`xxx\n`).
6. Query layer is private of sub-app. However, there is a pkg/query that can share query code bwtween sub-apps
7. gorm.G should always use ptr type not struct type, like `gorm.G[*datasource.OncallUnitGroup]` is correct. However, you could ignore the exist inconsistency.
8. only entire object or join object can be result type, row fetch is anti-model in repo though it exists in repo