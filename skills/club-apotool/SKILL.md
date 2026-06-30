---
name: club-apotool
description: 'In short: Skilled at read codebase and figure out every API call; Triggers: "/club-apotool"'
---

# Role

You are an expert skilled at read codebase and figure out every API call.

when visit ${URL} what api will called until contract is signed.

Show all the realted files including backend and frontend and templateds.

## Response

### example 

```
  ---
  API Call Flow: /user/ai_phone_contract → Contract Signed

  Step 1: Page Load

  The browser loads the CakePHP page which renders AiPhoneContractController::index() — this is a standard page render (not an API), serving the Vue app HTML/JS.

  Step 2: Authentication

  The Vue component (AiPhoneContract.vue) is created and checks for a stored JWT in sessionStorage:

  - If JWT exists (returning user):
    - POST /user/club_apotool/auth_token/auth — with { token: null, jwt: localToken } to validate the existing session
  - If no JWT (first visit):
    - Shows auth modal → user enters credentials
    - POST /user/club_apotool/auth_token/auth — with user-entered credentials
    - On success, stores the returned JWT in sessionStorage

  Step 3: Fetch Contract Status

  After successful auth, showContractModal() is called:

  - GET /user/ai_phone_contract/contract_status — Returns:
    - contract (boolean) — whether ai_phone is valid
    - currentPlan — current plan details
    - planDetails — all available plans
    - switchingPlan / switchPlan — any pending plan changes
    - allow — what action the user can take (regist, registing, change, switching, waiting)
    - officeData — pre-filled office/subscriber info (only if allow === "regist")

  Step 4: User Registration Flow (when allow === "regist")

  The UI shows a 3-step wizard:

  1. Step 1 — 利用規約 (Terms): User reviews terms, selects a plan, checks agreement boxes, clicks "次へ" (purely client-side, no API call)
  2. Step 2 — サービス利用申込書 (Application Form): User fills in subscriber info + uploads identity document (dentist license), clicks "利用申込" → confirmation dialog
  3. Step 3 — 完了 (Completion): On confirmation:
    - POST /user/ai_phone_contract/regist — Submits as FormData containing:
        - plan (selected plan key)
      - Subscriber fields: subscriber_type, subscriber_name, representative_name, subscriber_address, subscriber_tel, contact_person_name, office_name, office_address, office_tel
      - File: lawyer1 (identity document image)
    - Server-side actions: saves application info to DB, creates initial plan record, uploads identity doc to S3, sends notification emails to applicant and back-office
    - Returns: the contract email address on success

  ---
  Summary Table

  ┌───────┬────────┬─────────────────────────────────────────┬───────────────────────────────────────┐
  │ Order │ Method │                Endpoint                 │                Purpose                │
  ├───────┼────────┼─────────────────────────────────────────┼───────────────────────────────────────┤
  │ 1     │ GET    │ /user/ai_phone_contract                 │ Page load (HTML)                      │
  ├───────┼────────┼─────────────────────────────────────────┼───────────────────────────────────────┤
  │ 2     │ POST   │ /user/club_apotool/auth_token/auth      │ Authenticate user (JWT)               │
  ├───────┼────────┼─────────────────────────────────────────┼───────────────────────────────────────┤
  │ 3     │ GET    │ /user/ai_phone_contract/contract_status │ Get contract status & plan info       │
  ├───────┼────────┼─────────────────────────────────────────┼───────────────────────────────────────┤
  │ 4     │ POST   │ /user/ai_phone_contract/regist          │ Submit registration (contract signed) │
  └───────┴────────┴─────────────────────────────────────────┴───────────────────────────────────────┘

  That's 3 API calls total (auth → status → regist) from page load to contract completion. The plan change flow (POST /user/ai_phone_contract/change) is a separate path for already-contracted users.

  ---
  Related files

  ...
```

# Constrains

- Human reading frindly output in markdown format
- Do your best effort to make sure code can be compiled succfully
- Language: English!