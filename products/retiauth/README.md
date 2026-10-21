# RetiAuth

[RetiPlatform overview](../../README.md) · [Identity and tenancy](../../platform/identity-and-tenancy.md) · [Mobile architecture](../../platform/mobile-architecture.md)

**Identity and authentication services, with a device-local mobile authenticator.**

RetiAuth provides the identity foundation behind platform login, multifactor authentication, company-aware sessions, OAuth client access, and a developing delegated-authorization capability.

Its mobile authenticator generates time-based one-time passwords (TOTP) for accounts enrolled through a supported QR code or manual setup key. Saved accounts, code generation, and local unlocking work independently of a RetiPlatform login. The mobile app is built with Flutter and Dart, with Android and iOS native integration.

This page describes implemented development capabilities and recorded validation. The public repository contains documentation, not the application source, installable builds, credentials, or authenticator data.

## Where RetiAuth fits

The authenticator and the platform identity system have different responsibilities:

| Responsibility | Owner |
| --- | --- |
| Store authenticator entries locally and generate rotating codes | RetiAuth mobile app |
| Unlock access to local entries | Operating-system device authentication, integrated through shared mobile adapters |
| Verify a Reti account's password and MFA, issue and revoke sessions | Shared platform identity services |
| Decide access to a company, product, or business record | Backend membership, entitlement, role, and resource checks |

A rotating code is an authentication factor, not a grant of access to every Reti product. Unlocking the local vault also does not authorize a server request. See [identity and tenancy](../../platform/identity-and-tenancy.md) for the wider platform model.

RetiAuth can hold supported third-party TOTP accounts without creating a Reti account. Importing an ordinary authenticator QR code never signs the user in to RetiPlatform.

## Server identity and authorization capabilities

The server foundation supports human sign-in and registered software clients through shared services:

| Capability | Current implementation |
| --- | --- |
| Platform login | Password verification, TOTP verification, temporary-password handling, and application-session creation. |
| Application authorization | Authorization-code completion for the platform's registered application login flow. |
| Company-aware SSO | Session restoration with the destination company's membership, entitlement, and role context. |
| OAuth client administration | Platform-admin-protected confidential-client registration with hashed client secrets and configured grant, audience, scope, and lifetime restrictions. |
| Machine access | Client-credentials grants for independently authenticated registered clients. |
| Delegation grants | An authenticated user can create a short-lived, single-use grant for an allowed client, audience, and scope set. |
| Delegated token exchange | Exchange an authorized grant for a signed token retaining the represented user and actual client actor as distinct identities. |

The delegation model allows a software actor to use bounded authority without receiving the user's reusable password or MFA seed. Its current foundation enforces client, audience, scope, and lifetime constraints. The broader tenant/environment policy model, complete delegated audit integration, generic browser handoff, and support-consent administration remain separate development work.

The platform's existing login flows and these server capabilities do not yet constitute a complete standalone OAuth/OpenID Connect product interface. Standalone authorization and user-information surfaces still contain unfinished implementation paths. See [identity and tenancy](../../platform/identity-and-tenancy.md) for the surrounding access model.

## Authenticator capabilities

| Capability | Implemented behavior |
| --- | --- |
| QR setup | Scan a supported `otpauth` TOTP QR code, validate its settings, review the account, and save it locally. Invalid and unsupported payloads produce an explicit error. |
| Manual setup | Enter a service, account label, and Base32 setup key, with supported algorithm, digit, and period settings. |
| Offline code generation | Generate codes from the saved secret and device clock without a backend request. Display the current code and its remaining lifetime. |
| Account list | Show service and account labels, optional project grouping, and current verification codes in one list. |
| Search and organization | Search by service, account, or project, and filter by project. |
| Metadata editing | Rename the displayed service or change the project without replacing the setup key or algorithm settings. |
| Duplicate handling | Reject duplicate setup keys and duplicate issuer/account pairs instead of silently overwriting an existing entry. |
| Deliberate copying | Copy a current verification code through the native privacy adapter; setup keys are not offered as clipboard content. |
| Account removal | Require confirmation before removing a local entry. Removal does not disable MFA at the originating service. |
| Device unlock | Use the operating system's biometric or device-credential prompt. A device screen lock is required; there is no separate application PIN or unlocked fallback. |
| Lifecycle locking | Remove protected screens and unfinished drafts on backgrounding, and lock after two minutes without interaction. A late unlock result cannot override a later lock. |

The parser supports TOTP with SHA1, SHA256, or SHA512; six or eight digits; and periods from 15 to 120 seconds. HOTP and unrelated QR payloads are outside the implemented format. Correct device time remains necessary for codes to match the service verifying them.

## Optional Reti account integration

The local authenticator remains usable without platform sign-in. The **Reti account** screen separately provides the shared mobile password/MFA, session restoration, and logout experience. Logging out locks local access but preserves saved authenticator entries, so those codes remain available after the next device unlock.

Reti web enrollment can also connect the authenticator to a platform session through an explicit, optional flow:

1. The user completes web activation and password setup, then scans the website's MFA registration QR in RetiAuth.
2. RetiAuth presents the account for review. When signed out, **Also sign in to RetiAuth** is available and off by default. A user who is already signed in can save another code without replacing the existing platform session.
3. The app saves the account. The user manually enters its rotating code on the website to verify MFA; scanning or saving the QR does not complete authentication.
4. Only after the website verifies that transaction can the opted-in app finish platform sign-in. The app does not automatically submit the rotating code.

The connected flow uses a short-lived, one-use enrollment grant and proof bound to the originating transaction and environment. The app checks server-provided account details against the scanned provisioning information before saving the connected enrollment. Browser and mobile completion remain separate exchanges.

Interrupted setup can resume through **Finish website verification**. **Keep code without app sign-in** abandons pending app sign-in while retaining saved authenticator entries. Expired or unrecoverable enrollment requires a fresh authorized flow or normal password/MFA sign-in; it does not imply that local codes were lost.

Current mobile platform-session issuance is restricted to the development environment. This implemented integration is not a production authentication rollout.

## Implementation and data boundaries

RetiAuth is a product application within the shared Flutter/Dart workspace. Its screens call product services; storage, device authentication, privacy controls, and network session behavior are exposed through shared adapters. This keeps authenticator behavior separate from platform transport and native plugin details.

| Component | Responsibility |
| --- | --- |
| QR parser | Validate the supported provisioning format, labels, secret encoding, and code settings before persistence. |
| TOTP service | Generate standards-based codes through an existing OTP library and calculate countdown boundaries. |
| Authenticator service | Own unlocked state, account operations, duplicate checks, and serialized persistence; prevent delayed work from reopening locked content. |
| Protected repository | Store the account collection and any pending enrollment as a versioned protected local value, without a separate plaintext account index. |
| Onboarding service | Coordinate proof creation, server verification, protected persistence, resumption, and explicit cancellation. |
| Shared identity and UI packages | Supply device-authentication and storage adapters, themes, account controls, and reusable presentation. |
| Shared network package | Handle HTTPS transport, environment selection, server-validated platform sessions, renewal, and revocation. |

Android storage uses the shared encrypted-storage adapter backed by Android Keystore. The iOS adapter uses passcode-protected, device-local Keychain storage. Storage failures remain visible rather than automatically resetting unreadable data. Application unlocking is an operating-system authentication gate over protected storage; it is not a claim that every individual vault read requires biometric-bound decryption.

Privacy behavior is platform-specific. Android blocks screenshots by default and offers an explicit capture override limited to the current unlock session. iOS provides an app-switcher privacy cover rather than a general screenshot-blocking guarantee. Code copying uses sensitive-clipboard handling on Android and local-only expiring pasteboard items on iOS. Android cleanup is requested within 20 seconds or code expiry, but operating-system background limits can delay it.

The [shared mobile architecture](../../platform/mobile-architecture.md) explains how reusable packages support independent products without duplicating identity or transport logic.

## Validation and delivery status

Recorded evidence distinguishes algorithm tests, simulated UI journeys, native device behavior, and release readiness:

| Area | Recorded coverage and limits |
| --- | --- |
| TOTP and parsing | Automated tests cover RFC 6238 test vectors, leading zeroes, countdown boundaries, short-key interoperability, malformed QR input, and unsupported settings. |
| Account behavior | Unit and widget tests cover account creation, editing, deletion, duplicate rejection, persistence errors, lock/save races, search, and large-text layouts. |
| Android development journeys | Emulator records cover the local account experience, QR recognition through the emulator camera, offline codes, and preserved-data upgrades. |
| Connected enrollment | The October 2026 browser/emulator record covers optional sign-in, local-only enrollment, an already-signed-in account, shared-session regression, interrupted setup, and retained offline codes. This milestone did not include physical-phone acceptance. |
| iOS integration | The October 2026 Simulator record includes a normal debug build and launch, automated tests, and scanner UI exercised with an injected camera adapter. The native protected-vault/unlock journey was not completed; this is not physical-camera or iPhone acceptance. |
| Release readiness | Physical-device security and camera acceptance, relevant key-invalidation/reinstallation scenarios, release signing, production authentication, and store distribution remain separate gates. No independent security-audit certification is claimed. |

## Current limits

The implemented authenticator has no cloud backup, cross-device synchronization, export, or account-recovery service. Device-local entries should not be treated as recoverable through a RetiPlatform login. Uninstalling the app or changing device security can make entries unavailable; users need the originating service's recovery codes or another sign-in method.

Push approvals, passkeys, HOTP, and provider-specific account integrations are outside the current authenticator build. Further standalone identity-product administration, delegated-access policy, and production distribution remain development scope alongside the implemented server foundation, local authenticator, and development enrollment integration.

[Back to RetiPlatform](../../README.md)
