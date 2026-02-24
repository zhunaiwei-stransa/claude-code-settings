# code snippet

## router

#### `apps/receipt/internal/infrastructure/router/karte.go`
```go
karteRouter.GET("/latest-update-dates", func(c *echo.Context) error {
    transactionManager := rdb.NewTransactionManager(c.Request().Context(), s.dbs.ReceiptDB)

    karteRepository := persistence.NewKarteRepository(transactionManager)
    fetchLatestUpdateDatesUseCase := karteusecase.NewFetchLatestUpdateDatesUseCase(
        karteRepository,
        s.medicalDataClient,
    )
    fetchLatestUpdateDatesHandler := kartehandler.NewFetchLatestDatesHandler(
        fetchLatestUpdateDatesUseCase,
    )

    return fetchLatestUpdateDatesHandler.FetchLatestDates(c)
})
```

## handler

#### `apps/receipt/internal/handler/kartehandler/fetch_latest_dates_handler.go`
```go
func (h *fetchLatestDatesHandler) FetchLatestDates(c *echo.Context) error {
	officeID, err := echo.ContextGet[uint64](c, config.OfficeIDKey{}.Key())
	if err != nil {
		return httperror.InternalServerError(
			config.ErrCodeInternalServerError,
			fmt.Errorf("get office id: %w", err),
		)
	}

	in := &karteinput.FetchLatestUpdateDatesInput{
		OfficeID: officeID,
	}

	if err := c.Bind(in); err != nil {
		return httperror.BadRequest(
			config.ErrCodeBadRequest,
			fmt.Errorf("fetch latest dates bind: %w", err),
		)
	}

	out, err := h.fetchLatestDatesUseCase.FetchLatestUpdateDates(
		c.Request().Context(),
		in,
	)
	if err != nil {
		return fmt.Errorf("fetch latest dates use case: %w", err)
	}

	return c.JSON(http.StatusOK, out)
}
```

## vo

#### `vo/officevo/vo_id.go`
```go
type ID uint64

var (
	ErrInvalidOfficeID = errors.New("invalid office id")

	ErrInvalidEncodedOfficeID = errors.New("invalid encoded office id")
	ErrCouldNotDecode         = errors.New("could not decode")
	ErrOfficeIDIsRequired     = errors.New("office id is required")
)

func NewID(id uint64) (ID, error) {
	if id < 1 {
		return 0, ErrInvalidOfficeID
	}

	return ID(id), nil
}

func (i ID) Value() uint64 {
	return uint64(i)
}

func (i ID) Equals(id ID) bool {
	return i.Value() == id.Value()
}
```

## domain

#### `apps/receipt/internal/domain/kartedm/entity_karte.go`
```go
type Karte struct {
	id kartevo.ID

	officeID  officevo.ID
	patientID patientvo.ID
	staffID   resourcevo.ID

	globalID kartevo.GlobalID

	karteType          *kartevo.KarteType
	inputDate          sharedvo.InputDate
	inputDateUpdatedAt sharedvo.AuditTime

	createdAt sharedvo.AuditTime
	createdBy uservo.ID
	updatedAt sharedvo.AuditTime
	updatedBy uservo.ID
	deletedAt *sharedvo.AuditTime
	deletedBy *uservo.ID
}
```

## repository

#### `apps/calendar/internal/infrastructure/rdb/persistence/daily_memo_repository_impl.go`
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
