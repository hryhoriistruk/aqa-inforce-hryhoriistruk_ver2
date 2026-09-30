# aqa-inforce-hryhoriistruk_ver2




Automated tests for <https://automationintesting.online/> (Restful-booker platform):
UI tests for the User App (room booking) and API tests for the Admin / User flows.

The same test cases (`../aqa-inforce-hryhoriistruk_ver2/test-cases.txt`) are automated twice:

| Framework | Folder | Run from |
|---|---|---|
| **Cypress** (JavaScript) | `cypress` | repository root |


## Test cases (where to find them)

| What | File | Location |
|---|---|---|
| Manual test cases (UI + API), defects and observations | `../aqa-inforce-hryhoriistruk_ver2/test-cases.txt` | repository root |
| UI automation - Cypress (TC-UI-01…08, TC-UI-10…15) | `user-spec.cy.js` | `../aqa-inforce-hryhoriistruk_ver2/cypress/e2e` |
| API automation - Cypress (TC-API-01…09) | `admin-spec.cy.js` | `../aqa-inforce-hryhoriistruk_ver2/cypress/e2e` |



Manual only: TC-UI-09 (calendar mouse-drag; the same booking flow is automated in TC-UI-01).
Known application defects are described in `../aqa-inforce-hryhoriistruk_ver2/test-cases.txt` (BUG-01…03 and OBS-01…06).

TC-UI-08 ("earlier booked dates show as Unavailable") is automated in three checks: the calendar
data feed, the "cannot be booked again" behaviour, and the calendar drawing itself. The drawing
fails because of BUG-03 (the calendar does not draw earlier booked days), so that check is a
**known-bug test**: `xfail` in pytest (XPASS once the application is fixed) and pending in
Cypress (run it with `CYPRESS_RUN_KNOWN_BUGS=true npm run test:ui`).

`cy.intercept` (Cypress) and `page.expect_response` (Playwright) are used to check the
requests behind the UI (booking POST, room list, calendar feed).

---

## Cypress

Requirements: Node.js 18+.

```bash
git clone https://github.com/hryhoriistruk/aqa-inforce-hryhoriistruk_ver2.git
cd aqa-inforce-hryhoriistruk_ver2
npm ci
```

```bash
npm test               
npm run test:ui        
npm run test:api       
npm run test:headed    
npm run cy:open        
CYPRESS_RUN_KNOWN_BUGS=true npm run test:ui   
```

Base URL and admin credentials are set in `../aqa-inforce-hryhoriistruk_ver2/cypress.config.js` and can be overridden, e.g.
`CYPRESS_BASE_URL=... CYPRESS_apiUrl=... npm test`.

## Python (Playwright + pytest)

Requirements: Python 3.9+ (CI runs the suite on 3.9, 3.10, 3.11 and 3.12).





## Repository structure

```
aqa-inforce-hryhoriistruk_ver2
├── .github
│   └── workflows
│       ├── python-ci.yml        
│       └── cypress-ci.yml       
├── cypress
│   ├── downloads                
│   ├── e2e
│   │   ├── user-spec.cy.js      
│   │   └── admin-spec.cy.js     
│   ├── fixtures                 
│   └── support                  
├
├
├
├── test-cases.txt
├── cypress.config.js
├── package.json
├── package-lock.json
└── README.md
```

## Notes

- Every test creates its own room with a unique name through the Admin API and deletes it
  (with its bookings) afterwards, so tests are independent and do not pollute the shared site.
- The demo site is shared and reset periodically - if a test flakes, simply re-run it
  (or rely on the automatic retries in CI).
- API tests accept both response codes where the public gateway and the room service differ
  (200/202, 401/403); see OBS-03 in `../aqa-inforce-hryhoriistruk_ver2/test-cases.txt`.

## CI

GitHub Actions, triggered on push to `main`, on pull requests to
`main`, and manually via `workflow_dispatch`:

- **`cypress-ci.yml`** — `npm ci` + Cypress run in Chrome; screenshots are uploaded as an
  artifact on failure.
