# code snippet

## handler

### code 1

```go
// apps/calendar/internal/handler/psproducthandler/fetch_ps_suggestion_for_print_handler.go

package psproducthandler

type fetchPsSuggestionForPrintHandler struct {
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
// apps/calendar/internal/usecase/psproductusecase/psproductinput/fetch_ps_suggestion_for_print.go

package psproductinput

type FetchPsSuggestionForPrintInput struct {
	OfficeID  uint64
	PatientID uint64 `query:"patient-id" validate:"required"`
	Date      string `query:"date"       validate:"timeDate"`
}
```

### code 3: output

```go
// apps/calendar/internal/usecase/psproductusecase/psproductoutput/fetch_ps_suggestions.go

package psproductoutput

import "github.com/stransa-co-ltd/receipt-backend/apps/calendar/internal/query/psproductquery"

type FetchPsSuggestionsOutput struct {
	PsSuggestion      *FetchPsSuggestionsItem      `json:"psSuggestion,omitempty"`
	PsSuggestionLines []FetchPsSuggestionsLineItem `json:"psSuggestionLines"`
}

type FetchPsSuggestionsItem struct {
	ID         uint64  `json:"id"`
	Conditions *string `json:"conditions"`
}

type FetchPsSuggestionsLineItem struct {
	ID          uint64 `json:"id"`
	PsProductID uint64 `json:"psProductID"`
}
```

## Points and pitfalls

1. one handler, one file, one API
2. handle function process: officeID, in, echoapi.BindAndValidate, out, c.JSON
3. input defined in input pkg
4. output defined in output pkg