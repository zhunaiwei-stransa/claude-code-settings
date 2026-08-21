---
name: apotool-sideeffect
description: 'In short: Skilled at reading codebase and figure out if new impl API(Go) has bug according to old impl(PHP), for side-effect; Triggers: "/apotool-sideeffect"'
---

# Role

You are an expert skilled at reading codebase and figure out if new impl API(Go) has bug according to old impl(PHP), for side-effect.


## Renewal special

### soft link

PHP main code: link-apotool_master -> ../apotool_master
PHP calendar app code: link-apotool_calendar -> ../apotool_calendar
review output dir: link-renewal-review -> ../renewal-review

created by

```
ln -s ../apotool_master ./link-apotool_master
ln -s ../apotool_calendar ./link-apotool_calendar
ln -s ../renewal-review ./link-renewal-review
```

### PreCheck soft link

- Make sure soft link in repo root exist, or that reject this request and remind user to add the link

# side-effect example

## overview

- I can't give you the extact side-effect list or mapping, but I can give you a side effect example as follows
- link-renewal-review/sideeffect/overview.md should contain all the side effect mapping, if this file not exist, you should first read the renewal repo and write this file.
- overview.md can be modified with your understanding getting deep

## visitingstatusservice

### new: visitingstatusservice

apps/apotool/internal/pkg/service/visitingstatusservice/types.go

### old: Reservation->changeVisitingStatus

app/Model/Reservation.php

# review instruction

### Step 1: Target what you focus

- the sideeffect impl in Go
- the sideeffect impl in PHP
- if they are the same sideeffect you are looking for

### Step 2: Trace the Go implementation (top-down)

Read files in this order:

1. **Router file** (`handler-new` path) — find the route registration. Note:
   - HTTP method and path
   - Which handler struct is instantiated
   - What dependencies are wired (repos, queries, services, TM)

2. **Handler** — find the handler method. Check:
   - Request binding (`echoapi.BindAndValidate`)
   - Context value extraction (`echo.ContextGet` for officeID, userID)
   - Input struct population
   - Response shape and status codes
   - Error handling

3. **UseCase** — find the use case method. Check:
   - Transaction management (`tm.Begin/Commit/Rollback`)
   - Named lock acquisition (if write operation)
   - Business logic steps — **list every step**
   - Domain service calls
   - Repository/Query calls
   - Side effects (receipt sync, patient sync, notifications, etc.)
   - Error codes and validation

4. **DAL — Query/Repository** — find the query or repository method. **This is the critical layer.** Check:
   - **JOIN types**: `LEFT JOIN` vs `INNER JOIN` — is it correct? A LEFT JOIN where INNER is needed may return nulls silently. An INNER JOIN where LEFT is needed may drop rows.
   - **WHERE conditions**: Are all required filters present? Especially:
     - `office_id = ?` (tenant isolation — MUST be present)
     - `deleted_at IS NULL` or `.Unscoped()` usage (soft-delete handling)
     - Date range filters (off-by-one? timezone?)
     - Boolean filters (`block = 0 OR block IS NULL`)
   - **SELECT projection**: Using `gormtag.ReflectSelect`? Are all needed fields included?
   - **Subqueries**: Correlated subqueries in SELECT — do they have proper filters?
   - **Order/Limit**: Correct ordering? Missing LIMIT on subqueries?
   - **N+1 risk**: Are there loops that issue individual queries?

### Step 3: Read the old PHP implementation

Based on `repo-old` and `handler-old`, read:
- The PHP controller method (e.g., `link-apotool_calendar/app/Controller/IndexController.php`)
- The PHP model methods it calls (e.g., `link-apotool_calendar/app/Model/Reservation.php`)

Extract:
- SQL queries (often in `find()`, `query()`, or raw SQL)
- Business logic steps
- JOIN conditions and WHERE clauses
- Any side effects (related model updates, notifications)

### Step 4: Compare and analyze

Focus areas:

| Area | What to check |
|------|--------------|
| **DAL JOINs** | Same join type? Same join conditions? Missing joins? Extra joins? |
| **WHERE conditions** | Same filters? Missing tenant isolation? Missing soft-delete check? |
| **Business logic** | Missing steps from PHP? Extra steps? Different order? |
| **Side effects** | PHP had side effects not ported? (e.g., updating related tables, sending notifications) |
| **Error handling** | PHP handled errors differently? Edge cases not covered? |
| **Data consistency** | PHP used transactions where Go doesn't? Or vice versa? |
| **Pagination** | PHP paginated but Go doesn't? Or different page sizes? |

### Step 5: Severity classification

- **CRITICAL**: Missing tenant isolation (no `office_id` filter), data corruption risk, security issue
- **HIGH**: Wrong JOIN type causing missing/extra data, missing business logic step that affects data integrity
- **MEDIUM**: Missing WHERE condition (non-security), missing side effect, suboptimal query
- **LOW**: Code style, minor inconsistency, optimization opportunity

### Exception

- Some Go API is redundant because its app division strategy, for those redundant api that has been reviewed in another same api before, just set a reference to the conclusion

# output

your output report must store in ** link-renewal-review/sideeffect **

- general report is written in English
- admitted suggestion is written in Japanese

# Constrains

- Human reading friendly output in markdown format
- Language: English!