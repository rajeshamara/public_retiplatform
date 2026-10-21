# Identity and tenancy

[Platform architecture](README.md) | [RetiPlatform home](../README.md)

RetiPlatform separates the person who signs in from the companies, products, and records that person may access. A shared identity allows several products to recognize the same user; each authorized company context carries its own access decisions.

## The access model

| Decision | What it establishes |
| --- | --- |
| Platform identity | Who the authenticated user is. |
| Company membership | Whether the user is an active member of the selected company. |
| Product entitlement | Whether that company has the selected product enabled. |
| Product role | Which actions the user may perform in that product and company. |
| Resource permission | Whether the user may act on the particular matter, document, request, or other record. |

Company-local internal/external classification is separate from product roles. The same person can have different relationships with different companies. A role assignment should not silently redefine that relationship.

For example, access to one company's legal matters does not authorize access to another company's signing requests. The destination company, product entitlement, role, and resource policy must all be evaluated.

## Company databases and product schemas

The data architecture uses a central platform registry and a database per company. Product schemas organize business records within each company's database. Shared identity and membership records connect the authenticated user to the allowed company contexts.

Each client therefore has its own business database within a shared application architecture. The application runtime and central identity services serve multiple clients; the design does not imply a dedicated application server or database server for every client. The [security and client isolation chapter](security-and-isolation.md#one-business-database-per-client) illustrates the data boundaries.

Product-schema presence is separate from product entitlement. A database may contain the structures needed for a product before the company is authorized to use it.

Ordinary tenant operations select a database through validated session/request context. A supplied URL, email domain, document reference, or storage path does not independently authorize a database switch. Product boundaries still apply even when two schemas are physically in the same database.

## Sessions and company context

The shared web session establishes identity, while company/product context determines where the user is operating. Application startup restores authenticated state before mounting protected screens. Per-origin company/product context supports separate authorized browser tabs without treating one tab's company selection as permission for another.

The shared authentication-cookie helper sets `HttpOnly`, `Secure`, and `SameSite=Strict`. Signed company/product context is bound to the parent session and the requesting origin. Product subdomains identify the intended experience; the server evaluates authorization for that context. See [security and client isolation](security-and-isolation.md) for the cookie controls, subdomain model, and integration-specific differences.

Single sign-on carries identity into an authorized destination. The destination still checks active membership, entitlement, and roles. Browser logout expires the parent authentication cookie and the derived context cookies received with the request; derived context cannot authenticate without a valid parent. Local display state alone is insufficient to authenticate a request.

The mobile foundation provides protected session storage, server validation during restoration, refresh, logout, company selection, and optional local device unlock. Its current development authentication flow and release target are explained in [mobile architecture](mobile-architecture.md).

## Onboarding and administration

The shared onboarding model resolves or creates a platform identity, establishes the intended company membership, provisions the local representation, assigns authorized product roles, and completes account activation where required. Existing credentials and unrelated memberships should survive onboarding into an additional company.

Company administrators and product administrators have different scopes. Company administration applies across the company's entitled products; product administration is limited to the selected product. Platform operation is a separate authority.

Onboarding is a business process with intermediate states. Submitting it is distinct from completed membership, activation, and usable access. The implementation includes shared onboarding and role-management surfaces, while alignment and validation of all product journeys remain incremental.

## RetiAuth and shared identity

RetiAuth has two related aspects in this platform: reusable authentication and authorization services, and a product experience that includes a mobile authenticator. The authenticator stores accounts and generates time-based one-time codes locally; platform sign-in and enrollment use shared server-side identity capabilities.

Generating a local code is separate from receiving an authenticated platform session. Optional biometric or device authentication unlocks protected local material; server authorization still controls access to company data. See the [RetiAuth product page](../products/retiauth/README.md) for current functionality and validation scope.

## Delegated authorization direction

The delegated-authorization design preserves two identities: the user being represented and the actor performing the operation. It is intended to support bounded services, workflows, support tools, and integrations without sharing the represented user's reusable login credentials.

The foundation includes OAuth client and token-exchange services. Broader policy administration, customer consent experiences, browser handoff, and complete product adoption require their own implementation and runtime evidence. This is an evolving capability, rather than a claim that every background action already uses the complete delegation model.

## Implementation and validation scope

This chapter describes the shared access architecture and selected existing mechanisms. Adoption and validation of the contract are tracked per product and journey; see the product pages for material limits.

Meaningful validation checks both permitted and denied behavior: missing or revoked membership, absent entitlement, an inappropriate role, a mismatched company context, an unassigned record, and an authorized transition into another company. A successful login proves identity; it does not establish all those access properties.

The [automated testing chapter](testing-and-quality.md) describes existing Playwright journeys and focused backend tests for session, cookie, company-context, and role behavior.
