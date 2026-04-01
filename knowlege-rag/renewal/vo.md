# code snippet

## vo

### code 1

```go
// vo/officevo/vo_id.go

package officevo

import (
	"errors"

	"github.com/stransa-co-ltd/receipt-backend/domain/cipherdef"
)

type ID uint64

var (
	ErrInvalidOfficeID = errors.New("invalid office id")
)

func NewID(id uint64) (ID, error) {
	if id < 1 {
		return 0, ErrInvalidOfficeID
	}

	return ID(id), nil
}

func NewNullableID(id *uint64) (*ID, error) {
	if id == nil {
		return nil, nil
	}

	idVO, err := NewID(*id)
	if err != nil {
		return nil, err
	}

	return &idVO, nil
}

func (i ID) Value() uint64 {
	return uint64(i)
}

func (i *ID) NullableValue() *uint64 {
	if i == nil {
		return nil
	}

	val := i.Value()

	return &val
}
```

### code 2: utils

```go
// vo/sharedvo/vo_audit_time.go
func NewAuditTimeNow() AuditTime {
	return AuditTime(time.Now().UTC())
}
```

## Points and pitfalls

1. vo is a wrapper of underlying type in datasource field
2. New$Voname always return (vo, error), no matter if error is nesessary
3. NewNullable$Voname always return (*vo, error), no matter if error is nesessary
4. New retuned err is a errors.New var like example
5. Value() and NullableValue() to get underlying value
6. If this vo is used in entity in pointer, you must generate NewNullable$Voname function
7. When you want to create a *vo, you may confused that use New and get address or use NewNullable, it usually depends on the input filed is pointer or value

## Constrains

1. Never swallow error when new a vo
2. For some legacy reason, domain field name may diff from table name, which means some field name is an alias for table. But vo should always use table name. Known alias(left name sholud never exist in vo): staff = resource;