# code snippet

## service

### code 1: types.go

```go
type Service interface {
	CreateOutcomesByKarte(ctx context.Context, input *CreateByKarteInput) error
	RemoveOldOutcomesByKarte(ctx context.Context, input *RemoveOldOutcomesByKarteInput) error
	FindStartAndEndKartesByBlocks(
		ctx context.Context,
		input *FindStartAndEndKartesByBlocksInput,
	) (*FindStartAndEndKartesByBlocksOutput, error)
	FetchOutcomesByBlocks(ctx context.Context, input *FetchOutcomesByBlocksInput) (*FetchOutcomesByBlocksOutput, error)
	RemoveOutcomesByBlocks(ctx context.Context, input *RemoveOutcomesByBlockInput) error
	CreateOutcomeByBlockItem(ctx context.Context, input *CreateOutcomeByBlockItemInput) error
	UpdateOutcomeByBlockItem(ctx context.Context, input *UpdateOutcomeByBlockItemInput) error
	RemoveOutcomesByBlockItems(ctx context.Context, input *RemoveOutcomesByBlockItemInput) error
	HandleOutcomesByKarteChanges(ctx context.Context, input *HandleOutcomeChangesByKarteInput) error
}

type service struct {
	karteRepository            kartedm.Repository
	receiptPatientRepository   receiptpatientdm.Repository
	monthlyOperationRepository monthlyoperationdm.Repository
	medicalDataClient          masteridclientdm.Service

	// For better reuse per request
	onceCured                  sync.Once
	curedReferenceIDsError     error
	curedTreatmentReferenceIDs []kartevo.ItemReferenceID
	curedCommentReferenceIDs   []kartevo.ItemReferenceID

	// Track which year months have been marked as changed (to avoid duplicate updates)
	markedYearMonths map[sharedvo.YearMonth]struct{}
}
```

### code 2: method

```go
type FetchOutcomesByBlocksInput struct {
	OfficeID  officevo.ID
	PatientID patientvo.ID
	Blocks    []*kartedm.Block
}

type FetchOutcomesByBlocksOutput struct {
	Outcomes map[kartevo.BlockID]kartevo.Outcome
}

func (s *service) FetchOutcomesByBlocks(
	ctx context.Context,
	input *FetchOutcomesByBlocksInput,
) (*FetchOutcomesByBlocksOutput, error)
```

### code 3: usage

```go
	outcomeRouter.POST("", func(c *echo.Context) error {
		transactionManager := rdb.NewTransactionManager(c.Request().Context(), s.dbs.ReceiptDB)
		karteRepository := persistence.NewKarteRepository(transactionManager)
		receiptPatientRepository := persistence.NewReceiptPatientRepository(transactionManager)
		monthlyOperationRepository := persistence.NewMonthlyOperationRepository(transactionManager)

		outcomeService := outcomeservice.NewService(
			karteRepository,
			receiptPatientRepository,
			monthlyOperationRepository,
			s.medicalDataClient,
		)
		updateKarteOutcomeUseCase := karteusecase.NewUpdateKarteOutcomeUseCase(
			transactionManager,
			karteRepository,
			receiptPatientRepository,
			outcomeService,
		)
		updateKarteOutcomeHandler := kartehandler.NewUpdateKarteOutcomeHandler(updateKarteOutcomeUseCase)

		return updateKarteOutcomeHandler.UpdateKarteOutcome(c)
	})
```

## Points and pitfalls

1. Service is newed and called by usecase to share code between usecases
2. Similar to usecase, each service have independant package
3. Service interface and name is only Service/service that are in `types.go`, which is kind of special
4. Service method implement and input and output are in one seperate file.
5. We try our best to use vo as parameter for service method.
6. Service could have some helper functions, no matter public or private.

## Constrains
- The new service is only using repository but not query, even though it use join in repository. Others are temporary
- When wire a service, use nil params are totally forbidden, must new dependency even though it will not be used inside