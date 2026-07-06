# code snippet

## handler

### code 1

```go
package psproducthandler

type fetchPsSuggestionForPrintHandler struct {
	fetchPsSuggestionForPrintUseCase psproductusecase.FetchPsSuggestionForPrintUseCase
	fetchPsSuggestionForPrintUseCase psproductusecase.FetchPsSuggestionForPrintUseCase
}

func NewFetchPsSuggestionForPrintHandler(
	fetchPsSuggestionForPrintUseCase psproductusecase.FetchPsSuggestionForPrintUseCase,
) *fetchPsSuggestionForPrintHandler {
	return &fetchPsSuggestionForPrintHandler{
		fetchPsSuggestionForPrintUseCase: fetchPsSuggestionForPrintUseCase,
	}
}

func (h *fetchPsSuggestionForPrintHandler) FetchPsSuggestionForPrint(c *echo.Context) error {
	officeID, err := echo.ContextGet[uint64](c, requestdetail.OfficeIDKey{}.Key())
	if err != nil {
		return httperror.InternalServerError(
			config.ErrCodeInternalServerError,
			fmt.Errorf("get office id: %w", err),
		)
	}

	in := &psproductinput.FetchPsSuggestionForPrintInput{}
	if err := echoapi.BindAndValidate(c, in); err != nil {
			return httperror.BadRequest(config.ErrCodeBadRequest, err)
	}

	in.OfficeID = officeID

	out, err := h.fetchPsSuggestionForPrintUseCase.FetchPsSuggestionForPrint(c.Request().Context(), in)
	if err != nil {
		return fmt.Errorf("print ps suggestion use case: %w", err)
	}

	return c.JSON(http.StatusOK, httpresponse.NewSuccess(out))
}
```

### code 2: input

```go
package timelineinput

type FetchDailyInput struct {
	OfficeID uint64
	Date     string                  `query:"date" validate:"required,timeDate"`
	RawIDs   string                  `query:"ids"`

	IDs []uint64
}
```

### code 3: output

```go
package psproductoutput

import "github.com/stransa-co-ltd/receipt-backend/apps/calendar/internal/query/psproductquery"

type FetchPsSuggestionsOutput struct {
	PsSuggestion      *FetchPsSuggestionsItem      `json:"psSuggestion"`
	PsSuggestionLines []FetchPsSuggestionsLineItem `json:"psSuggestionLines"`
}

type FetchPsSuggestionsItem struct {
	ID         uint64  `json:"id"`
	Conditions *string `json:"conditions"`
}

type FetchPsSuggestionsLineItem struct {
	ID          uint64 `json:"id"`
	PsProductID uint64 `json:"psProductId"`
}
```

### code 4: patch API input

```go
type UpdateMaterialSettingDateInput struct {
	OfficeID uint64
	ID       uint64 `param:"id" validate:"required"`

	SettingDate echotype.NullSetter[string] `json:"settingDate"`

	SettingDateTime     *time.Time
}
```

### code 4: NullSetter

```go
package echotype

import (
	"encoding/json"
)

type NullSetter[T any] struct {
	value *T
	isSet bool
}

func (n *NullSetter[T]) UnmarshalJSON(data []byte) error {
	// enter UnmarshalJSON means that json has this field
	n.isSet = true
	if string(data) == "null" {
		n.value = nil
		return nil
	}

	var val T
	if err := json.Unmarshal(data, &val); err != nil {
		return err
	}

	n.value = &val

	return nil
}

func NewNullSetter[T any](val T) NullSetter[T] {
	return NullSetter[T]{value: &val, isSet: true}
}

func NewNullSetterNull[T any]() NullSetter[T] {
	return NullSetter[T]{isSet: true}
}

func (n *NullSetter[T]) Value() *T {
	return n.value
}

func (n *NullSetter[T]) IsSet() bool {
	return n.isSet
}
```


## Points and pitfalls

1. one handler, one file, one API
2. handle function process: officeID, in, echoapi.BindAndValidate, out, c.JSON
3. input defined in input pkg
4. output defined in output pkg
5. Id in json tag name is always Id, not ID, while for go lint, Go naming sholud be like ID
6. For input object, value type must have validate:"required"; But bool value no need, for example: PrivateExpense bool `json:"privateExpense"``
7. For input object, some params need second time pared, they are in the below area of input struct. And most importantly, they are all pared in the handler layer and be asigned to by var, and return httperror if parsed failed
8. For output object, when you want to do format change by type converting, youcan use buildXXX to do complicated struct creation. For simple field, you canuse GetXXX as getter to access field.
9. In patch API, when db col is nullable, we need to distinct whether FE give us a pointer or nothing to decide update this col or not. When col is not nullable, use raw pointer directly for patch. Don't need `IsChangeXXX` anymore.


## Constrains

- In `apps/calendar/internal/infrastructure/requestdto/`, defines some kinds of request format standard that most API should follow. For now it includes: menu, staff. For most recent message, you can list this directory.
- In `apps/calendar/internal/infrastructure/responsedto/icon.go`, defines some kinds of response format standard that most API should follow. For now it includes: menu, staff, patientStatus, icon, CheckedItem. For most recent message, you can list this directory.
- In every input, Memo sholud be decoded as base64 and store into MemoDecoded
- For Write API, return 204 like `return c.NoContent(http.StatusNoContent)`. Ignore exsisting 200 OK response.