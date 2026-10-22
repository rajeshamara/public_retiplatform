# Automated testing and quality

[RetiPlatform home](../README.md) · [Platform architecture](README.md)

RetiPlatform combines automated backend and frontend checks with Playwright browser journeys that exercise shared services through product interfaces. The aim is to detect failures in observable behavior: whether logout actually ends a session, a company switch restores the correct permissions, or a client sees the right document reminder.

This page describes implemented test infrastructure and representative cases. Execution examples are historical records from development environments, not a fresh test run or a claim that every product, role, browser, and device is covered. The private test harness and its fixtures are not distributed in this documentation repository.

## Test layers

| Layer | Implementation | Representative checks |
| --- | --- | --- |
| Backend unit and contract | Python and pytest with controlled dependencies | Company-context selection, cookie boundaries, MFA enrollment, role resolution, and workflow definitions. |
| Backend functional | FastAPI test clients exercising application routers | Logout with expired, missing, or malformed credentials; cookie expiration when server cleanup fails. |
| Frontend unit and component | Vitest, React Testing Library, and jsdom | Loading and error states, logout retries, session restoration, workflow expansion, and navigation behavior. |
| Browser journeys | Playwright's Node.js library attached to prepared Chrome sessions | Cross-product logout, company switching, intake notices, workflow navigation, and document download/review. |
| Mobile | Flutter/Dart unit and widget tests, supplemented by installed-app validation | Authenticator enrollment, code parsing and generation, shared networking, account screens, and platform-specific journeys. |
| Documents and forms | Targeted data, persistence, download, and rendered-output checks | Saved form structure, reload behavior, applicant scope, and the actual generated artifact. |

These layers answer different questions. A mocked component test can reliably reproduce a failed request; a real browser journey checks how the rendered application, session, navigation, and services work together. Mobile device acceptance and downloaded-document inspection remain separate from web component coverage.

Cookie tests assert Secure, HttpOnly, SameSite, matching domain/path, and expiration attributes. Company-session tests reject mismatched origins, missing or expired parent sessions, and unauthorized destination roles.

## Registered cases and repeatable runners

The formal testing system connects a machine-readable case catalog to human-readable specifications, automated implementations, and execution summaries. A case declares its product, feature, risk, lifecycle state, test levels, and intended gates. Its specification records starting conditions, steps, expected results, cleanup, and objective pass/fail criteria.

A catalog validator checks required metadata, unique identifiers, supported risk/status values, and referenced files. This provides traceability; catalog validity does not mean the associated tests have passed.

The fast aggregate runner validates the catalog, runs the formal pytest suite, and then runs the formal Vitest suite, stopping on failure. Pytest uses strict configuration and marker checking. Vitest uses explicit unit, component, and integration filename patterns, resets mocks, and supports V8 coverage output. Selected catalog entries also reference existing specialized tests outside the central directories, which require their own targeted execution.

Environment-dependent browser journeys run separately after application services, test data, and browser sessions are prepared. The inspected entry points are developer-operated runners and scripts. Catalog labels such as merge, nightly, and release express intended gate selection; they do not establish a scheduled CI service. The full login account matrix remains marked automation-ready, while several focused browser regressions are registered as automated.

## Playwright browser automation

Authenticated automation uses dedicated browser profiles for platform administration, attorneys, external clients, tenant users, and signing participants. Setup helpers prepare the selected profile, perform automated password/MFA login using approved test-account configuration, and verify browser state before the journey runs. Account changes can clear authentication and origin storage to avoid reusing another identity's session. Public landing pages use a separate unauthenticated mode.

The implemented validators use Playwright's Chromium connection over the Chrome DevTools Protocol. They interact with visible controls, wait for meaningful elements or responses, inspect session metadata, and fail with a nonzero process exit when assertions fail. This is primarily Chrome-based development acceptance; it is not evidence of an exhaustive Chromium, Firefox, and WebKit compatibility matrix.

Representative automated journeys include:

- **Shared logout:** open product tabs through the Apps menu, sign out from one product, and assert that other tabs return to login. Checks include exactly one logout request, removal of authentication cookies and cached identity, unauthorized SSO responses, and continued logout after reload.
- **Company-session restoration:** compare a user's personal signing lists across company contexts while checking distinct company roles. The journey injects stale cached identity, checks denied sender and document access, downloads an actual PDF, opens the review screen, and returns to RetiLaw. It stops before submitting consent or a signature.
- **Workflow navigation:** expand nested workflow definitions individually and together, collapse the tree, check definition-request failures, verify reusable labels, and compare an earlier definition. Component tests separately exercise failed branches and repeated workflow instances.
- **RetiLaw intake notices:** verify client-assigned intake, pending document review, mapping navigation, and reload. Playwright request interception supplies controlled missing-document, absent-intake, and service-failure responses to check reminders, error presentation, and prevention of editing incompletely loaded state.

The intake runner labels intercepted and simulated scenarios separately from live checks and removes its interceptors afterward. A simulated outage validates UI behavior; it does not prove a real backend outage was exercised.

## Evidence and regression practice

Representative browser runners save screenshots and structured JSON results. The signing journey also saves the downloaded PDF and checks its file signature. Frontend coverage can produce text, HTML, JSON, and LCOV reports. Small execution summaries record the selected cases, environment, revision context, results, and limitations; generated artifacts remain separate from source documentation.

Recorded September 2026 runs include three-way shared logout, company-session restoration with access-denial checks, and intake-notice validation. Those records identify their tested roles and environments. The intake record also preserves unrelated baseline failures excluded from its final targeted run, rather than presenting the broader suite as entirely passing.

The testing standard requires controlled synthetic or approved local data, explicit tenant and role preconditions, and exclusion of credentials, raw session tokens, MFA seeds, and production client data from evidence. Missing prerequisites or quarantined cases must remain visible rather than silently becoming passes.

Regression selection follows the affected contract: add the cheapest reliable check at the appropriate layer, then exercise existing consumers when shared behavior changes. UI changes require real browser validation, while form changes require saved-state and output checks. See [security and isolation](security-and-isolation.md), [identity and tenancy](identity-and-tenancy.md), [documents and forms](documents-and-forms.md), and [mobile architecture](mobile-architecture.md) for the boundaries these tests help protect.

The [development infrastructure](../infra/README.md) supplies the machines, worktree isolation, private connectivity, and device environments behind these checks. [AI-assisted development](../infra/ai-assisted-development.md) describes agent qualification and task evidence; [mobile validation](../infra/mobile-validation.md) separates simulator, physical-device, and release acceptance.
