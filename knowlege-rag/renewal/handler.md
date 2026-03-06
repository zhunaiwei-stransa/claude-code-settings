# code snippet

## handler

### code 1

```go
// apps/calendar/internal/handler/psproducthandler/fetch_ps_suggestion_for_print_handler.go

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

	in := &psproductinput.FetchPsSuggestionForPrintInput{
		OfficeID: officeID,
	}
	if err := echoapi.BindAndValidate(c, in); err != nil {
		return httperror.BadRequest(config.ErrCodeInvalidParameter, err)
	}

	out, err := h.fetchPsSuggestionForPrintUseCase.FetchPsSuggestionForPrint(c.Request().Context(), in)
	if err != nil {
		return fmt.Errorf("print ps suggestion use case: %w", err)
	}

	return c.JSON(http.StatusOK, httpresponse.NewSuccess(out))
}
```

### code 2: input

```go
// apps/calendar/internal/usecase/timelineusecase/timelineinput/fetch_daily.go

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
// apps/calendar/internal/usecase/psproductusecase/psproductoutput/fetch_ps_suggestions.go

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

## Points and pitfalls

1. one handler, one file, one API
2. handle function process: officeID, in, echoapi.BindAndValidate, out, c.JSON
3. input defined in input pkg
4. output defined in output pkg
5. Id in json tag name is always Id, not ID, while for go lint, Go naming sholud be like ID
6. For input object, value type must have validate:"required"; But bool value no need, for example: PrivateExpense bool `json:"privateExpense"``
7. For input object, some params need second time pared, they are in the below area of input struct. And most importantly, they are all pared in the handler layer and asigned to in var, and return httperror if parsed failed
