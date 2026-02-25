# code snippet

## query

### code 1: query interface

```go
// apps/calendar/internal/query/pssuggestionquery/ps_suggestion_query.go
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
// apps/calendar/internal/infrastructure/rdb/persistence/queryimpl/ps_suggestion_query_impl.go

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

## Points and pitfalls

1. Query is defined in independent pkg, and implemented in package queryimpl
2. Repository and Query are seperate concept; Repository belongs to domain, but Query bypasses the domain
