# code snippet

## service

### code 1

```go
// apps/calendar/internal/service/visitingstatusservice/types.go

type Service interface {
	ChangeVisitingStatus(ctx context.Context, params ChangeVisitingStatusParams) (reservationdm.VisitingStatus, error)
}

type service struct {
	reservationRepository reservationdm.Repository
	patientRepository     patientdm.Repository
	officeRepository      officedm.Repository
	oncallRepository      oncalldm.Repository
	oncallQuery           oncallreservationquery.Query
}

func NewService(
	reservationRepository reservationdm.Repository,
	patientRepository patientdm.Repository,
	officeRepository officedm.Repository,
	oncallRepository oncalldm.Repository,
	oncallQuery oncallreservationquery.Query,
) Service {
	return &service{
		reservationRepository: reservationRepository,
		patientRepository:     patientRepository,
		officeRepository:      officeRepository,
		oncallRepository:      oncallRepository,
		oncallQuery:           oncallQuery,
	}
}
```

## Points and pitfalls

1. service is newed and called by usecase to share code between usecases
2. similar to usecase, each service have independant package