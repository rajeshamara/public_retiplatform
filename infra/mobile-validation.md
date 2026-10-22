# Mobile development and device validation

[RetiPlatform home](../README.md) · [Infrastructure](README.md) · [Mobile architecture](../platform/mobile-architecture.md)

RetiPlatform's mobile development environment combines shared Flutter/Dart development, an Android emulator and physical phone, and a separate Apple toolchain for iOS. The infrastructure supports a repeatable path from source checks to installed-app behavior, with evidence attached to the product, build, device, and network route actually exercised.

Android development is established on Linux. iOS builds and Simulator validation have also been exercised on a Mac, with more limited product acceptance. The dated results below describe recorded development milestones with acceptance limited to the tested product and environment.

## Development roles and lanes

| Machine or execution host | Lane | Mobile responsibility and evidence boundary |
| --- | --- | --- |
| `minipc` | `worktree32` | Flutter/Dart and Android development, the mobile development API, shared emulator work, and recorded Samsung phone validation. |
| `devmac` | `worktree50` | Mac/iOS development lane. Its configured assignment identifies where work belongs; it does not establish that a particular iPhone journey passed. |
| Mac mini, October 17, 2026 execution | `worktree51` | Recorded Connector and RetiAuth iOS Simulator work using a candidate from `worktree32`, with additional iOS project integration. |

Lane assignments evolve, and a source-development lane can differ from the host used for platform validation. The October execution record therefore identifies its actual Mac lane rather than attributing that result to every Mac checkout.

Linux owns the normal Flutter editing, Android command-line builds, and Android testing loop. The Mac supplies Xcode, Apple SDKs, Simulator runtimes, native dependency integration, and the later signing and distribution workflow. Windows serves as a controller and optional graphical client. It is not required to compile Android apps, and Linux source or test success does not establish an iOS build result.

Git provides the source handoff between hosts. A meaningful comparison records the candidate revision and any uncommitted platform changes. Machine-local SDK paths, generated builds, caches, device state, and signing material remain outside the source contract.

## Android infrastructure

The Linux baseline uses Flutter, its bundled Dart SDK, a compatible JDK, Android command-line tools, and hardware-accelerated emulation through KVM. Android Studio is optional. Toolchain versions are recorded as a tested combination; upgrades require deliberate build and device validation rather than silently following a moving release channel.

One canonical Android Virtual Device is shared across the mobile suite. Independent application identities allow the products to coexist, making package, storage, native communication, and shared-session interactions visible during testing. Device access is serialized, and every operation targets the verified device rather than whichever phone or emulator happens to be available.

The emulator can run headlessly while still exposing rendered screenshots, UI hierarchy, input, installed-package metadata, and scoped logs. An on-demand Xfce remote desktop provides a visible emulator when interactive inspection helps. Quick Boot preserves the same virtual phone state between sessions.

Normal validation upgrades the installed app while preserving its data and then starts a fresh process. This catches retained preferences and stale-process problems that a clean reinstall could hide. Integration-test cleanup is checked explicitly, and the ordinary application entry point is restored after a test build.

A USB-connected Samsung phone supplies real-device evidence. The installation helper selects the designated authorized phone, verifies the APK belongs to the active checkout, and performs an upgrade installation. It does not silently substitute another device, build the app, or treat installation as a feature pass. Compatible signing identity is necessary for upgrades that preserve data.

## From build checks to user journeys

| Validation layer | What it establishes |
| --- | --- |
| Formatting, analysis, unit and widget tests | Deterministic source, service, algorithm, and presentation checks without a complete device environment. |
| Debug build and installation | Platform project and dependency integration, package generation, and installation on the selected target. |
| Emulator or Simulator journey | Real controls, navigation, state transitions, native integration where available, and rendered outcomes for the tested app. |
| Physical-device journey | Actual hardware, OS prompts, permissions, networking, and lifecycle behavior within the recorded scope. |
| Signed release and distribution | Release configuration, signing, entitlement enforcement, distribution installation, and store-specific requirements. |

Flutter integration tests exercise application controls. Device tooling supplies supporting screenshots, UI state, installation details, and crash logs. Service and plugin fakes provide controlled lower-level tests; an injected camera response remains different from optical QR recognition, and a visible OS authentication prompt is different from successful vault access.

Mobile network journeys use explicitly selected public HTTPS environments and normal system certificate trust. The phone's request must reach the agreed route and render the response in the app. A successful host-side probe is supporting evidence. It does not replace the installed app's request, failure handling, or retry behavior.

## Recorded device outcomes

| Date and scope | Observed result | Limit |
| --- | --- | --- |
| September 27, 2026 — RetiLaw Android emulator | Password/TOTP login, authorized matter lists, user switching, refresh, cold restoration, offline failure/recovery, and RetiPlatform-hosted RetiLaw passed. | An empty-list scenario was covered with a fake service; it was not claimed as live empty-account acceptance. |
| September 27, 2026 — physical Samsung phone | Standalone RetiLaw passed login and correctly scoped matters for firm and external-user identities, refresh, user switching, and cold restoration over Wi-Fi. | Physical hosted mode, a complete cellular acceptance run, and physical biometric certification were not established. |
| October 17, 2026 — Connector on iOS Simulator | Debug build/install/launch, real Dev and Prod Hello requests, environment persistence across restart and preserved-data installation, malformed-setting error handling, and recovery passed. | Broader cross-app settings, signed entitlement enforcement, and physical-device behavior were deferred. |
| October 17, 2026 — RetiAuth on iOS Simulator | Debug build/install/launch and the scanner interface with an injected camera adapter passed; automated unit/widget checks also passed. | Native unlock waited at the OS passcode prompt. Protected-vault journeys were incomplete; physical iPhone, real camera recognition, and connected onboarding were deferred. |

The October run also encountered an Xcode/Simulator component mismatch. Its recorded workaround allowed the prepared Simulator binary to run, while the component repair and native system-input path remained separate prerequisites. This record supports specific Simulator outcomes, not full iOS readiness for RetiAuth or RetiLaw.

## Evidence and release boundaries

Validation records identify the source revision, app version, build mode, artifact hash, target platform, preserved-data state, actions, and observed outcomes. Screenshots and logs support case-level results without publishing credentials, authenticator secrets, session tokens, or customer data. Temporary device settings are restored, and the tested normal app remains available for review.

Production authentication, release signing, signed Apple capabilities, physical-iPhone acceptance, TestFlight, and store distribution require their own evidence. RetiLaw's inspected Android release configuration still uses debug signing, so a development APK is not a production release artifact. Successful Connector networking also does not certify another product's authentication or protected storage.

Continue with [mobile architecture](../platform/mobile-architecture.md) for shared packages and product boundaries, or [automated testing and quality](../platform/testing-and-quality.md) for the broader validation approach.
