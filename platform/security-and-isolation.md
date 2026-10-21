# Security and isolation

[RetiPlatform home](../README.md) | [Platform architecture](README.md) | [Identity and tenancy](identity-and-tenancy.md) | [Testing and quality](testing-and-quality.md)

RetiPlatform combines shared identity services, protected browser cookies, company-specific application context, and a database per company. These mechanisms address different boundaries: authenticating a person, selecting an authorized company and product, and permitting an operation on a particular record.

This chapter describes implemented shared mechanisms and the authorization contract they support.

## One business database per client

Each client company has its own database, created from a common company template. Products organize their business records into schemas within that database. A central platform registry maintains shared identity, company membership, and product entitlements.

This is **single-tenant business-data storage within a shared application architecture**: one business database per company, served by a shared application runtime. It does not imply a dedicated application server, virtual machine, database server, or compute allocation for each client. Product schemas may exist before a product is enabled; entitlement checks determine whether the company may use that product.

The intended protected-request sequence is:

```mermaid
flowchart LR
    Browser[Company and product web app] --> Identity[Validate signed identity and context]
    Identity --> Authority[Check membership, entitlement, role and resource]
    Authority --> Service[Application service]
    Service -->|Authorized company A context| A[(Company A database)]
    Service -->|Authorized company B context| B[(Company B database)]
    Registry[Central identity and company registry] --> Authority
```

The diagram shows logical request and data boundaries. Ordinary company services consume validated request context. Platform administration has separately bounded authority to manage companies.

## Product subdomains and company context

Company and product addresses make the selected workspace explicit. Illustrative addresses are `company-a.law.example.com` and `company-a.sign.example.com`. The product component selects the application experience; the company component identifies the destination the user intends to enter.

The company database has an immutable internal identity. A later change to the company's public subdomain does not rename that database. Server-side metadata and session handling connect the selected address to the appropriate company; a URL, email suffix, document reference, or storage address is not permission to access its data.

HTTPS sibling subdomains can be **same-site while cross-origin**. An origin includes scheme, host, and port; a site uses scheme and registrable domain. Consequently, SameSite cookie policy does not itself isolate products or companies hosted beneath one parent domain. [Browser site and origin definitions](https://web.dev/articles/same-site-same-origin)

## Browser cookie controls

The shared authentication-cookie helper sets **HttpOnly**, **Secure**, **SameSite=Strict**, and `Path=/`. It resolves the cookie domain from environment configuration so related application hosts can participate in the shared sign-in experience. The same helper issues signed company-context cookies. It retains those attributes in development as well as production; environment-specific domain selection does not automatically relax them.

| Cookie purpose | Current policy |
| --- | --- |
| Shared web identity | HttpOnly, Secure, Strict, with the configured shared domain and explicit path. |
| Company/product context | The same protected attributes, with a distinct cookie name derived from the application origin. |
| Signing invitation session | HttpOnly and Secure, with Lax SameSite behavior and a signing-specific path. It supplements the signing authorization flow. |
| External document integration | HttpOnly; SameSite defaults to Lax and is configurable, as is Secure behavior. Its policy is separate from platform sign-in. |

HttpOnly prevents page JavaScript from reading the cookie; the browser can still attach it to JavaScript-initiated requests. Secure restricts transmission to secure transport. Strict limits sending to same-site requests, while Lax permits certain cross-site navigations. These attributes reduce particular risks rather than establish application permissions. [Cookie attribute semantics](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Set-Cookie)

Integration, demo, and diagnostic cookies have their own settings. Cookie scope and browser retention are also distinct from the signed session's expiry and authorization rules.

## Validated sessions and independent company tabs

The web application restores server-validated session state before mounting ordinary protected product screens. Cached browser profile information supplies display state; it does not establish authentication. The application does not need JavaScript access to the authentication cookie to ask the server whether the session is valid.

Company entry checks active membership and product entitlement in the central registry before reading the destination company's effective roles. A successful transition issues a signed context containing the destination company and application origin.

That context remains bound to the parent login's identity, session, scope, and expiry. Verification checks its signature and relationship to the parent; a context cookie alone cannot authenticate the user. Origin-specific names keep authorized company tabs from replacing each other's selection. Routes using the shared authentication dependency also reject a mismatched application origin.

The main logout flow expires the shared identity cookie and received company-context cookies using their issuance scope. Server-side cleanup is attempted separately. This describes browser logout behavior, not a guarantee that every independently issued credential is immediately revoked everywhere.

## Authorization and request boundaries

Authentication identifies the caller. Product and resource services decide what that caller may do. Implemented examples include RetiSign's active-membership, entitlement, role, and document-assignment checks, and role administration's distinction between platform, company, and product authority. A visible menu or a successful sign-in does not replace these checks.

Request-local context carries the selected database. The request lifecycle clears this routing state after each request, keeping database selection scoped to that request. Authorization remains the responsibility of the applicable authentication dependencies and business services.

SameSite policy and configured CORS origin handling contribute browser-side restrictions. They do not replace current permissions or establish a complete, uniform cross-site request forgery defense. Route-level review and negative tests remain necessary; this implementation is not described as having a universal CSRF-token mechanism.

## Validation and development scope

Security acceptance needs both permitted and denied journeys: valid session restoration, expired or altered sessions, company-context mismatch, missing membership or entitlement, unsuitable roles, unassigned records, company switching, and logout. Existing automated cases cover shared-cookie attributes, logout, and company-session behavior. See [testing and quality](testing-and-quality.md) for browser and backend coverage and execution limits.

Adoption of the shared authorization contract is validated per product and journey. For example, [RetiBot's command architecture](retibot-intent-execution.md) records unfinished ownership and authorization work. The mechanisms described here do not establish complete endpoint coverage or production acceptance.
