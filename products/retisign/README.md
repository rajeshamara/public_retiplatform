# RetiSign

[RetiPlatform overview](../../README.md) · [Workflow engine](../../platform/workflow-engine.md) · [Identity and tenancy](../../platform/identity-and-tenancy.md) · [Documents and forms](../../platform/documents-and-forms.md)

**PDF preparation, guided electronic signing, and verifiable completion evidence on RetiPlatform.**

RetiSign connects a sender's document workspace with a signer's personal inbox. Senders prepare an agreement, place fields, assign eligible recipients, and track its progress. Signers review the PDF, record consent, complete their fields, and submit an explicit signing decision. Completed agreements remain owned by the sending company, with authorized access through the signer's document dashboard.

RetiSign is an independent product using shared platform capabilities; it does not require RetiLaw. This page describes the current private development implementation. The public repository contains documentation, and individual features have different levels of integration and validation.

## Sender workspace

The preparation flow follows **document → fields → users**. A sender can design the document against placeholder signer slots before choosing the actual people. Preparation and dispatch require those slots to resolve to eligible, onboarded RetiSign users in the sending company.

| Capability | Current behavior |
| --- | --- |
| PDF preparation | Upload a source PDF, preview its pages, resume a saved draft, and organize an envelope around its subject. |
| Field placement | Click to place fields, drag to move them, resize their rectangles, adjust positions with the keyboard, and remove or reassign fields. Recipient colors help distinguish ownership. |
| Field types | Signature, initials, signing date, name, email, text, checkbox, radio choice, dropdown choice, and attachment. |
| Field rules | Required fields, choice options, validation patterns for supported text fields, and conditional visibility controlled by a checkbox. |
| Repeated layouts | Copy a placed field to the same position on other pages, and save or apply reusable field-layout templates mapped by signer order. |
| Recipient setup | Select eligible company users, assign fields to signer slots, and configure access codes. The underlying envelope services also support ordered recipients and carbon-copy recipients. |
| Dispatch and tracking | Prepare and freeze the signing definition, submit invitations through workflow orchestration, inspect recipient and workflow progress, and retry a submission that needs attention. |
| Follow-up controls | Send reminders, resend invitations, void an envelope, or create a corrected draft while retaining the original envelope's history. |
| Completed documents | Preview the executed agreement and completion certificate; download those PDFs and the evidence manifest, and inspect the associated evidence summary. |

## Signer experience

**To Sign** and **Signed Documents** provide personal lists across the companies where the user has current authorized access. The inbox includes due-date indicators and a due-soon filter. Company cards show pending and completed counts and the next due date.

These lists use the signer's personal document-reference index and platform membership information. Opening a document or signing step enters the document owner's verified company context, where the server checks current membership, product role, and document assignment again. Source-PDF access and signing additionally require the corresponding claim and session proof. A document reference or storage address is not an access grant.

The signing ceremony has four addressable steps:

1. **Review:** open the agreement and affirm consent to electronic records and signatures.
2. **Fields:** move through assigned required fields in page order, enter values, supply requested attachments, and adopt a typed or drawn signature with initials.
3. **Confirm:** review the signing decision and explicitly confirm intent before submission.
4. **Done:** see the recorded result and, when the envelope is complete, access the final agreement and certificate.

Signers can also decline a request. Terminal envelope states prevent further signing. Account identity, recipient access-code verification, session checks, and rate limits protect the claim and signing flow. Sender permissions, signer permissions, and administration remain separate capabilities; an administrator is not automatically entitled to sign or send.

## Workflow and completion evidence

RetiSign applies the [shared workflow engine](../../platform/workflow-engine.md) to a concrete process that waits for a person:

**Prepare → dispatch → wait for signature → finalize artifacts → publish the signed-document reference → submit completion notifications.**

For a new envelope with **one PDF and one signer**, this sequence runs as one persisted workflow. Its waiting state binds the assigned signer, session, source document and digest, and frozen workflow definition. Resume requires the committed signature and consent evidence; a caller-supplied completion claim cannot replace that proof. Retry paths can continue unfinished preparation or reconcile an already committed signature after interrupted dispatch.

Broader envelopes use the existing initial-send workflow and product-specific signing, completion, and job services. Those paths include recipient ordering and completion after the required recipients finish; they have not all been migrated into the single shared lifecycle.

The evidence package connects the source document, signing actions, and final output:

| Evidence component | Purpose |
| --- | --- |
| Hash-linked audit events | Link ordered envelope events to their preceding event and retain actor, session, and action context. |
| Executed PDF | Render the completed fields and signature representations into the final agreement. |
| Completion certificate | Present a readable record of the signing event and document evidence. |
| Signed manifest | Record document digests and evidence references in canonical JSON, signed with Ed25519. |
| Artifact storage | Write content-addressed artifacts with create-only storage operations and verify stored generations and digests during completion and retrieval. |
| Evidence verifier | Verify the detached manifest signature and optionally compare an uploaded executed PDF's SHA-256 digest with the manifest. |

This verification mechanism is a signed evidence manifest accompanying the PDF. It does not imply that the PDF carries a certificate-based PDF digital signature.

## Operations and shared platform services

The operations dashboard shows queued or running jobs, failed or exhausted jobs, delivery failures, completing envelopes, publication backlog, and approaching expirations. Operators can process a due job, publish pending signer-library events, and queue due expiration work. Durable jobs retain attempt counts, retry timing, and safe failure metadata. Configurable company sending quotas are enforced by the service and displayed when applicable in the sender workspace and operations dashboard.

The implementation uses React/TypeScript product screens, Python API and service layers, database-backed envelope and job records, and object storage for documents and personal references. It reuses the platform's [identity and company boundaries](../../platform/identity-and-tenancy.md), [workflow execution](../../platform/workflow-engine.md), [PDF viewer and document services](../../platform/documents-and-forms.md), and notification infrastructure.

## Current development boundary

Recorded development validation includes sender preparation, the guided signing ceremony, decline, personal dashboards, company-context entry, and a successful single-signer lifecycle.

Full multi-signer migration into the shared lifecycle, worker failure during active execution, and automatic delivery recovery are outside the completed single-signer acceptance scope. Workflow completion means the final artifacts and reference were persisted and completion notifications were submitted; it does not establish receipt in a recipient's mailbox. These boundaries describe the current development state rather than a general-availability or certification claim.
