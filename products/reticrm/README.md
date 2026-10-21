# RetiCRM

**Customer relationships, marketing audiences, sales pipelines, and vendor operations built on RetiPlatform.**

[RetiPlatform overview](../../README.md) · [Shared platform](../../platform/README.md) · [Identity and tenancy](../../platform/identity-and-tenancy.md)

RetiCRM connects the records behind acquisition and follow-up: organizations, contacts, campaign responses, leads, opportunities, and assigned work. It also includes vendor and purchasing capabilities that connect purchase orders, received items, payable invoices, and payment records.

The product is under active development. This page describes capabilities represented in the private application's current screens, APIs, services, and data contracts. It is a documentation showcase, not an application distribution or a general-availability announcement.

## Current capabilities

| Area | Implemented scope |
| --- | --- |
| Accounts | Searchable organization records with account type, lifecycle stage, domain, industry, employee count, ownership, and related contacts. |
| Contacts | Contact records linked to accounts, lifecycle and contact status, communication preferences, and channel-specific consent evidence. |
| Marketing | Saved audience definitions, segment previews, persisted list membership, campaign planning, audience eligibility, and response recording. |
| Leads | Search and filtering, qualification, ratings, notes, follow-up activities, campaign attribution, and conversion into an account/contact/opportunity relationship. |
| Sales pipeline | Configurable pipelines and stages, opportunity movements, won/lost closure, probability-weighted values, stage aging, and change history. |
| Vendors | Vendor records, lifecycle transitions, contacts, performance events, and a workspace for related purchasing and payment records. |
| Purchasing | Purchase-order approval and issue, linked receipt capture, payable invoices, three-way matching, and controlled outgoing-payment record completion. |
| Financial records | Expenses, receipts, invoices, and incoming/outgoing payment records with related account or vendor references. |
| Reporting and operations | Campaign performance, lead conversion, pipeline health, vendor spending, purchase-match exceptions, assigned tasks, duplicate review, and data-quality findings. |

## From audience to opportunity

Marketing starts with structured account and contact information. Audience filters can select contacts by lifecycle stage, account industry, employee-count range, and prior campaign status. A preview reports matched, eligible, and suppressed contacts before the audience is saved as list membership.

Consent is recorded as evidence with a channel, decision, source, and effective time. The supported channels are email, phone, and SMS, with an all-channel option. This evidence drives audience eligibility decisions, and a contact without applicable consent is not treated as eligible by default. Contact records also retain communication preferences.

Campaign records hold the objective, channel, dates, cost information, and attribution metadata. Operators attach eligible list members, inspect the current audience, and record responses. A responded or converted member can produce a lead while preserving the campaign relationship. Campaign management here means planning, audience selection, and recorded engagement; it does not establish an integrated bulk-delivery service for email or SMS.

Lead workspaces bring together qualification, version history, notes, and activities such as calls, emails, meetings, and to-dos. Qualification records a status, rating, owner, and reason. Conversion requires a qualified lead and explicit choices about reusing existing accounts and contacts. The conversion service creates or reuses the selected records and creates the opportunity as one transaction. Repeating the same completed conversion can return the existing result instead of creating another set of records.

## Configurable sales pipelines

Pipelines organize opportunities into ordered stages. Each stage has a stable identity, display name, probability, and open/won/lost classification. Open stages can define a threshold for identifying stalled opportunities. Pipeline types include sales, renewal, and partner.

An opportunity carries its account, primary contact, value, currency, expected close date, owner, and source information. The pipeline screen supports movement between open stages and dedicated closing actions. Lost outcomes require a reason. Stage changes retain a version history, and stale edits are rejected so an older screen cannot silently replace a newer decision.

Pipeline summaries report raw and probability-weighted value. Reporting adds stage aging, stalled counts, and won/lost outcomes. These are calculations over recorded CRM data, rather than model-generated sales forecasts.

## Vendor and purchasing operations

Vendor management covers onboarding, active, under-review, and offboarded states. Records include category, payment terms, risk rating, and contact information. A vendor workspace presents contacts, recorded performance events, version history, purchase orders, payable invoices, and payment records together.

The purchasing path connects the records required for a three-way match:

1. Create a purchase order for an active vendor with item quantities and amounts.
2. Approve and issue the order through explicit state transitions.
3. Capture receipts against identified purchase-order lines, updating received quantities and order status.
4. Record a payable invoice with explicit links to the purchasing records.
5. Compare ordered, received, and invoiced quantities and amounts, retaining match results and exception reasons.
6. Complete the outgoing payment record only when its payable invoice has an active matched result.

Matching currently uses exact quantities and amounts. Missing orders, missing receipts, quantity differences, and amount differences produce explicit results. This is not a configurable tolerance or automatic discrepancy-resolution system. The current purchasing screen provides a focused item-entry flow; the underlying service contracts support line collections.

Payment completion updates CRM records. It does not itself transfer funds through a bank or payment processor. Expense and invoice tracking likewise does not imply a complete accounting ledger or tax system.

## Reporting and daily operations

The dashboard and reports combine relationship, sales, and vendor information:

- **Campaign performance:** planned and actual costs, attributed leads, conversions, won value, and calculated return measures.
- **Lead funnel:** lead counts by status and source, converted counts, and conversion rate.
- **Pipeline health:** stage values, weighted totals, time in stage, stalled opportunities, and closing outcomes.
- **Vendor spending:** separate committed, invoiced, and paid amounts, with performance events and purchasing exceptions.
- **Daily work:** the current operator's open and overdue tasks, recent activity, and links to records requiring attention.

The operations workspace also surfaces deterministic duplicate candidates for accounts, contacts, and leads. Matching rules use normalized identifiers such as email, domain, or name plus phone/postal information. An operator chooses the surviving record and reviews the fields and relationship counts before confirming a merge.

The merge service rechecks the matching rule, both record versions, and the confirmed survivor values. Supported relationships are moved transactionally and the source record is retired with history retained. Unsupported active relationships block the merge. This is a bounded, human-confirmed cleanup capability, not unrestricted automatic entity resolution.

Data-quality findings highlight conditions such as opportunities without a valid stage, payable invoices without a purchase order, missing vendor categories, and tasks without an owner.

## Platform integration and engineering

RetiCRM uses the shared React and TypeScript application, Python/FastAPI services, typed request and response models, and PostgreSQL persistence. Product screens call a typed CRM service, which uses the platform request transport. Backend routes delegate to CRM services and SQL modules. Runtime configuration registers product routes and role-specific navigation.

Protected CRM APIs use shared authentication and product-access checks. The current access model checks company entitlement and active local roles before supplying company context to the CRM services. Most record mutations require CRM administration authority; assigned-task completion additionally checks the operator and task state. Menu visibility does not substitute for these backend checks.

Versioned records retain stable identities while creating replacement versions for changes. Updates carry an expected version and a change reason. Related-record checks are scoped to the authenticated company, helping prevent an account, vendor, or invoice from being linked across company boundaries.

RetiCRM is independently composed over the [shared platform](../../platform/README.md). Integration across products belongs at the platform level; this page does not claim direct dependencies on RetiLaw or RetiSign. The platform's [workflow engine](../../platform/workflow-engine.md) is documented separately. The CRM operations described here are implemented service commands; their presence does not imply that every CRM lifecycle already runs through that engine.

## Development and validation scope

The private implementation includes registered CRM screens, backend routes, persistence queries, typed models, and contract tests covering successive CRM capability groups. This public description was checked against those source boundaries; it is not a fresh browser, database, or deployment acceptance report.

The current scope emphasizes explicit operator actions, traceable state changes, and connected business records. Broader automation, outbound campaign delivery, payment settlement, and release readiness require their own implementation and validation evidence before being described as delivered capabilities.
