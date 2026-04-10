# code snippet

## usecase

### code 1: update

```go
package cancelwaitingusecase

type UpdateCancelWaitingUseCase interface {
	UpdateCancelWaiting(ctx context.Context, in *cancelwaitinginput.UpdateCancelWaitingInput) error
}

type updateCancelWaitingUseCase struct {
	cancelWaitingRepository cancelwaitingdm.Repository
}

func NewUpdateCancelWaitingUseCase(
	cancelWaitingRepository cancelwaitingdm.Repository,
) *updateCancelWaitingUseCase {
	return &updateCancelWaitingUseCase{
		cancelWaitingRepository: cancelWaitingRepository,
	}
}

func (uc *updateCancelWaitingUseCase) UpdateCancelWaiting(
	ctx context.Context,
	in *cancelwaitinginput.UpdateCancelWaitingInput,
) error {
	officeIDVO, err := officevo.NewID(in.OfficeID)
	if err != nil {
		return httperror.BadRequest(
			config.ErrCodeInvalidParameter,
			fmt.Errorf("new office id: %w", err),
		)
	}

	idVO, err := cancelwaitingvo.NewID(in.ID)
	if err != nil {
		return httperror.BadRequest(
			config.ErrCodeInvalidParameter,
			fmt.Errorf("new cancel waiting id: %w", err),
		)
	}

	entity, err := uc.cancelWaitingRepository.GetCancelWaiting(ctx, officeIDVO, idVO)
	if err != nil {
		if errors.Is(err, cancelwaitingdm.ErrCancelWaitingNotFound) {
			return httperror.NotFound(config.ErrCodeNotFound, err)
		}

		return httperror.InternalServerError(
			config.ErrCodeInternalServerError,
			fmt.Errorf("get cancel waiting: %w", err),
		)
	}

	dateFrom, err := timeconv.ParseDateInUTC(in.DateFrom)
	if err != nil {
		return httperror.BadRequest(
			config.ErrCodeInvalidParameter,
			fmt.Errorf("parse date from in utc: %w", err),
		)
	}

	dateFromVO, err := cancelwaitingvo.NewDateFrom(dateFrom)
	if err != nil {
		return httperror.BadRequest(
			config.ErrCodeInvalidParameter,
			fmt.Errorf("new date from: %w", err),
		)
	}

	var dateToVO *cancelwaitingvo.DateTo

	if in.DateTo != nil {
		dateTo, parseErr := timeconv.ParseDateInUTC(*in.DateTo)
		if parseErr != nil {
			return httperror.BadRequest(
				config.ErrCodeInvalidParameter,
				fmt.Errorf("parse date to in utc: %w", parseErr),
			)
		}

		dateToVOValue, voErr := cancelwaitingvo.NewDateTo(dateTo)
		if voErr != nil {
			return httperror.BadRequest(
				config.ErrCodeInvalidParameter,
				fmt.Errorf("new date to: %w", voErr),
			)
		}

		dateToVO = &dateToVOValue
	}

	timeFromVO, err := cancelwaitingvo.NewTimeFrom(in.TimeFrom)
	if err != nil {
		return httperror.BadRequest(
			config.ErrCodeInvalidParameter,
			fmt.Errorf("new time from: %w", err),
		)
	}

	timeToVO, err := cancelwaitingvo.NewTimeTo(in.TimeTo)
	if err != nil {
		return httperror.BadRequest(
			config.ErrCodeInvalidParameter,
			fmt.Errorf("new time to: %w", err),
		)
	}

	requiredTimeVO, err := cancelwaitingvo.NewRequiredTime(in.RequiredTime)
	if err != nil {
		return httperror.BadRequest(
			config.ErrCodeInvalidParameter,
			fmt.Errorf("new required time: %w", err),
		)
	}

	var memoVO *cancelwaitingvo.Memo

	if in.Memo != nil {
		memoVOValue, memoErr := cancelwaitingvo.NewMemo(*in.Memo)
		if memoErr != nil {
			return httperror.BadRequest(
				config.ErrCodeInvalidParameter,
				fmt.Errorf("new memo: %w", memoErr),
			)
		}

		memoVO = &memoVOValue
	}

	var menuIDVO, menu2IDVO, menu3IDVO *resourcevo.ID

	if menu1ID := in.Menu.GetMenu1ID(); menu1ID != nil {
		menuIDVOValue, menuErr := resourcevo.NewID(*menu1ID)
		if menuErr != nil {
			return httperror.BadRequest(
				config.ErrCodeInvalidParameter,
				fmt.Errorf("new menu1 id: %w", menuErr),
			)
		}

		menuIDVO = &menuIDVOValue
	}

	if menu2ID := in.Menu.GetMenu2ID(); menu2ID != nil {
		menu2IDVOValue, menuErr := resourcevo.NewID(*menu2ID)
		if menuErr != nil {
			return httperror.BadRequest(
				config.ErrCodeInvalidParameter,
				fmt.Errorf("new menu2 id: %w", menuErr),
			)
		}

		menu2IDVO = &menu2IDVOValue
	}

	if menu3ID := in.Menu.GetMenu3ID(); menu3ID != nil {
		menu3IDVOValue, menuErr := resourcevo.NewID(*menu3ID)
		if menuErr != nil {
			return httperror.BadRequest(
				config.ErrCodeInvalidParameter,
				fmt.Errorf("new menu3 id: %w", menuErr),
			)
		}

		menu3IDVO = &menu3IDVOValue
	}

	var staffIDVO, staff2IDVO, staff3IDVO *resourcevo.ID

	if staff1ID := in.Staff.GetStaff1ID(); staff1ID != nil {
		staffIDVOValue, staffErr := resourcevo.NewID(*staff1ID)
		if staffErr != nil {
			return httperror.BadRequest(
				config.ErrCodeInvalidParameter,
				fmt.Errorf("new staff1 id: %w", staffErr),
			)
		}

		staffIDVO = &staffIDVOValue
	}

	if staff2ID := in.Staff.GetStaff2ID(); staff2ID != nil {
		staff2IDVOValue, staffErr := resourcevo.NewID(*staff2ID)
		if staffErr != nil {
			return httperror.BadRequest(
				config.ErrCodeInvalidParameter,
				fmt.Errorf("new staff2 id: %w", staffErr),
			)
		}

		staff2IDVO = &staff2IDVOValue
	}

	if staff3ID := in.Staff.GetStaff3ID(); staff3ID != nil {
		staff3IDVOValue, staffErr := resourcevo.NewID(*staff3ID)
		if staffErr != nil {
			return httperror.BadRequest(
				config.ErrCodeInvalidParameter,
				fmt.Errorf("new staff3 id: %w", staffErr),
			)
		}

		staff3IDVO = &staff3IDVOValue
	}

	entity.ChangeUpdatedAt(sharedvo.NewAuditTimeNow())

	entity.ChangeDateFrom(dateFromVO)
	entity.ChangeDateTo(dateToVO)
	entity.ChangeTimeFrom(timeFromVO)
	entity.ChangeTimeTo(timeToVO)
	entity.ChangeRequiredTime(requiredTimeVO)
	entity.ChangeWeekDays(in.WeekDays)
	entity.ChangeMemo(memoVO)
	entity.ChangeMenuID(menuIDVO)
	entity.ChangeMenu2ID(menu2IDVO)
	entity.ChangeMenu3ID(menu3IDVO)
	entity.ChangeStaffID(staffIDVO)
	entity.ChangeStaff2ID(staff2IDVO)
	entity.ChangeStaff3ID(staff3IDVO)

	err = uc.cancelWaitingRepository.UpdateCancelWaiting(ctx, entity)
	if err != nil {
		return httperror.InternalServerError(
			config.ErrCodeInternalServerError,
			fmt.Errorf("update cancel waiting: %w", err),
		)
	}

	return nil
}

```

### code 2: fetch single

```go
func (u *fetchCancelUseCase) FetchCancel(
	ctx context.Context,
	in *cancelinput.FetchCancelInput,
) (*canceloutput.FetchCancelOutput, error) {
	cancel, err := u.cancelQuery.FetchCancel(
		ctx,
		in.OfficeID,
		in.CancelID,
	)
	if err != nil {
		if errors.Is(err, canceldm.ErrCancelNotFound) {
			return nil, httperror.NotFound(
				config.ErrCodeNotFound,
				fmt.Errorf("cancel not found: %d", in.CancelID),
			)
		}

		return nil, httperror.InternalServerError(
			config.ErrCodeInternalServerError,
			fmt.Errorf("cancel not fetched: %w", err),
		)
	}

	return canceloutput.NewFetchCancelOutput(cancel), nil
}
```
### code 3: error handling

```go
	if err := k.transactionManager.Commit(); err != nil {
		return nil, fmt.Errorf("commit transaction: %w", err)
	}
```

### code 4: utils

```go
func ContractChecker(ctx context.Context) (*contractdm.Checker, bool) {
	checker, ok := ctx.Value(ContractCheckerKey{}).(*contractdm.Checker)
	if !ok || checker == nil {
		return nil, false
	}

	return checker, true
}
```

## Points and pitfalls

1. One handler, one file, one API
2. Wrap error with fmt.Errorf in any case
3. Update API use ChangeXXX update single field in memory, and at last update entity
4. Fetch single API check domain NotFound error
5. Error handle httperror sholud be beautifly formatted in several lines, not in a long line
6. Some unexpected err is not returned as httperror, for exmaple: Commit err;
7. Input may have some memory fields like memoDecoded, you should use these fields to create vo

## Constrains

- Usecase never parsed params into other type, this is all done in handler layer
- No need to comment any code in any case in any layer