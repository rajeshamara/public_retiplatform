# RetiLaw

[RetiPlatform home](../../README.md) | [Platform architecture](../../platform/README.md)

**A legal practice product connecting matters, documents, form preparation, workflow automation, and mobile access.**

RetiLaw brings matter information, supporting evidence, client and employer records, and team responsibilities into a connected application. Its current development focus is immigration practice, including employment-based matters, USCIS and Department of Labor form preparation, and assistance with the FLAG portal.

Immigration work moves the same information through records, documents, forms, reviewers, and external systems. RetiLaw organizes that work around authoritative data and persistent processes so teams can see what has been prepared, what is waiting, and which artifact needs review. Attorneys and firm staff remain responsible for consequential decisions.

## Product capabilities

| Area | Current implementation represented |
| --- | --- |
| Matter workspace | Attorney-facing matter screens, client and employer relationships, tasks, documents, and workflow visibility. |
| Document intake | Requests for supporting evidence, document collection, structured extraction, and review of information used downstream. |
| Form preparation | Shared generation and mapping services, applicant-specific data, saved form snapshots, preview, and PDF output. |
| Workflow orchestration | Service-backed agents, human tasks, nested processes, external-event waits, and execution tracking. |
| AI-assisted information access | Retrieval-augmented responses, chatbot integration, extraction, and common model-provider adapters. |
| Identity and access | Shared authentication and MFA, company membership, product permissions, and role-aware presentation. |
| Mobile access | Flutter app with shared account controls and an authorized, read-only matter directory. |
| Portal assistance | RetiLaw-to-FLAG transfer components with user review and controlled external-portal interaction. |

Individual components and complete legal workflows have different levels of integration and validation. This page describes product development rather than claiming every immigration scenario is fully automated.

## From matter information to a reviewed form

An illustrative legal process is:

1. Establish the matter and the relevant client, employer, and applicant information.
2. Identify missing evidence and send a document request.
3. Wait for the response, then extract and review relevant information.
4. Generate a form from authorized source records and actor-scoped manual answers.
5. Review the saved snapshot and the actual output artifact.
6. Complete the applicable approval before continuing the process.

The shared engine supplies execution, waiting, and resume mechanisms. Legal services and definitions supply the document requirements, applicant scope, permissions, and review rules. Concrete capabilities exist for onboarding, document requests, form generation, and notifications; broader immigration processes continue to be integrated and validated.

The complete [workflow-engine chapter](../../platform/workflow-engine.md) describes branching, loops, concurrency, child workflows, durable human tasks, external events, recovery, and monitoring.

## Forms, applicants, and evidence

Forms are prepared from structured information and persisted as versioned snapshots. Preview and output are designed to describe that same prepared version. Source mapping, canonical values, and PDF destinations have separate responsibilities, allowing shared generation services to support different form definitions.

Applicant identity is explicit where a matter includes a principal and dependents. Principal-only forms retain their own ownership requirements. Manual answers remain scoped to the relevant matter, form, and actor; reusable facts belong to their authoritative records.

Selected approval and artifact services retain the version and content identity of the work reviewed. This connects a decision to a particular prepared output instead of assuming the latest file is the file that was approved. See [documents and forms](../../platform/documents-and-forms.md) for the shared lifecycle and validation approach.

## Mobile app

RetiLaw's mobile app is built with **Flutter and Dart**, with Android and iOS project integration. Its current milestone provides authenticated, company-scoped matter visibility.

| Capability | Current development behavior |
| --- | --- |
| Sign-in | Shared native account controls with password and TOTP verification in the development authentication flow. |
| Company selection | Selection of an authorized company, with backend membership, entitlement, and role checks. |
| Matter directory | Authorized matter count and each matter's name, client, and employer where present. |
| Session handling | Protected storage, server validation on restoration, refresh, logout, and switching users. |
| Local unlock | Optional device authentication to unlock a protected existing session. |
| Refresh and recovery | Loading, empty, error, and retry states; failed reads clear stale matter data. |
| Reusable presentation | The same product screen runs standalone or inside the RetiPlatform mobile application. |

The current matter experience is **read-only**. Matter editing, detail actions, and offline matter caching are outside this milestone.

### How the app was developed

RetiLaw was developed as a thin product application over shared Flutter packages. Product-owned screens, typed matter models, and a product service consume shared networking and backend APIs. The backend reuses the existing matter-visibility rules, so the phone does not become another source of business authorization.

The work progressed from the app shell and shared workspace to real API connectivity, protected identity/session flows, and the first legal use case. Presentation includes deliberate loading and failure states, company changes, and refresh behavior. The same matter screen was then exercised through the standalone app and the platform host.

The [mobile-architecture chapter](../../platform/mobile-architecture.md) explains the shared packages, Android/iOS adapters, development milestones, and session design in detail.

### Mobile validation and delivery

Development records include Android emulator validation of standalone and RetiPlatform-hosted matter journeys, plus standalone testing on a physical Android phone over Wi-Fi. Those checks apply to the paths exercised.

The shared [mobile validation environment](../../infra/mobile-validation.md) supports Android and iOS development, including physical Android and iPhone testing. Current native mobile session issuance is development-only; the release target is system-browser OAuth Authorization Code with PKCE. Production authentication, release signing, and store distribution are separate delivery work.

## How RetiLaw uses RetiPlatform

RetiLaw supplies the legal domain: matters, form definitions, evidence requirements, applicant relationships, practice terminology, and workflow composition. Shared RetiPlatform capabilities provide identity, company access, documents, execution, communications, AI integration, and mobile infrastructure.

Future practice areas can add domain definitions and focused behavior while reusing those engines. Composition involving another product belongs to the platform, preserving RetiLaw's independent product boundary.

## Development status

RetiLaw is under active development. Its current focus combines immigration capabilities with the shared platform foundations needed for additional legal workflows and practice areas.

Form preparation, review, portal transfer, and actual external filing are distinct operations. A generated PDF or a workflow definition does not establish that a filing has been submitted or that every legal step is complete. Validation follows the applicable data, permission, browser, document-output, and device boundaries.
