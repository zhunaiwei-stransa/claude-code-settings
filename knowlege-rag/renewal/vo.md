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

## Points and pitfalls

1. vo is a wrapper of underlying type in datasource field
2. New$Voname always return (vo, error)
3. NewNullable$Voname always return (*vo, error)
4. Value() and NullableValue() to get underlying value