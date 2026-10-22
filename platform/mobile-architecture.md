# Mobile architecture

[Platform architecture](README.md) | [RetiPlatform home](../README.md)

The mobile suite uses **Flutter and Dart** in a shared workspace. Product apps own their screens and use cases while common packages provide configuration, UI, networking, protected identity, and session handling.

The current suite includes a RetiLaw matter experience and a RetiAuth authenticator. The RetiPlatform mobile app can host the same RetiLaw product screen used by the standalone application. Independent entry points let products retain their own identity and run without requiring companion-app installation.

## Shared packages

| Layer | Responsibility |
| --- | --- |
| Core | Platform-neutral types and runtime configuration contracts. |
| UI | Shared themes, account controls, and reusable presentation components. |
| Network | HTTP transport, authenticated requests, session orchestration, serialization, and transport errors. |
| Identity | Credential contracts, protected storage, and local device-authentication integration. |
| Environment configuration | Explicit environment selection and non-secret configuration through typed interfaces. |
| Product | Domain screens, typed product models, and product services. |

Requests follow **screen → product service → shared network service → backend API**. Product apps consume backend business capabilities; they do not access a database directly or recreate server authorization rules on the phone.

## Sessions and account controls

Shared controls support development sign-in with password and time-based one-time password verification, authorized company selection, session restoration, refresh, logout, and switching users. Restoring stored material requires server validation. Local device authentication can unlock an existing protected session, while server checks continue to determine product and record access.

Current native mobile session issuance is restricted to the development environment. The release authentication target is system-browser OAuth Authorization Code with PKCE. Development login behavior should not be mistaken for a completed production authentication release.

RetiAuth's local authenticator is a separate capability: an enrolled local account can generate codes offline without an active platform session. Its vault, locking, enrollment, and platform-specific behavior are described in the [RetiAuth page](../products/retiauth/README.md).

## Native integration

Shared Dart contracts isolate platform-specific operations. Android implementations include Keystore-backed protected storage, device authentication, and a signature-checked session broker using opaque references and native access controls. The broker supports authorized coordination between apps without relying on a plaintext token file shared between them.

iOS integration includes native identity adapters, Keychain storage, and application-group configuration. Shared Dart contracts keep those platform-specific details separate from product screens and business services.

## How the mobile foundation was developed

Development progressed through working increments:

1. **Product shells and workspace:** establish branding, independent entry points, shared packages, and common types.
2. **Real backend connectivity:** add explicit environment configuration, HTTPS requests, visible results, and error/retry states.
3. **Shared identity:** introduce MFA account controls, protected sessions, restoration, logout, and optional local device unlock.
4. **Product use cases:** add RetiLaw's authorized matter directory and RetiAuth's local authenticator with QR/manual provisioning and platform enrollment.
5. **Installed-app validation:** exercise the interface, session transitions, upgrades preserving existing data, network failures, and selected device journeys alongside automated checks.

RetiLaw uses typed models and an explicit product service over shared networking. Its Flutter widgets manage presentation and asynchronous state. RetiAuth adds authenticator-specific parsing, code generation, vault, onboarding, and privacy behavior behind focused services. Both benefit from common infrastructure while retaining different product responsibilities.

## Development and validation

Mobile development combines static analysis, unit and widget tests, Flutter integration tests, Android emulators, and the iOS Simulator. Physical Android and iPhone validation are part of the development process. Linux and Mac mini worktrees provide dedicated platform toolchains while sharing the same Flutter foundation.

Testing covers application behavior, session transitions, preserved-data upgrades, and network recovery. Build identities and test artifacts connect the results to the application version and selected device. Production authentication, release signing, and store distribution are managed as separate delivery work.

For product details, see [RetiLaw mobile](../products/retilaw/README.md#mobile-app) and [RetiAuth](../products/retiauth/README.md).

The [mobile development and device validation chapter](../infra/mobile-validation.md) explains the Linux and Mac mini toolchains, dedicated worktree assignments, emulator setup, preserved-data upgrades, and physical-device testing.
