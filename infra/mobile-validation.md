# Mobile development and device validation

[RetiPlatform home](../README.md) · [Infrastructure](README.md) · [Mobile architecture](../platform/mobile-architecture.md)

RetiPlatform's mobile development environment combines shared Flutter/Dart development with dedicated Android and iOS toolchains. Validation includes automated checks, emulators, and physical devices—including a Samsung Android phone and an iPhone.

Linux supports the Android development loop, while a Mac mini provides the Apple build and testing environment. Shared packages keep configuration, networking, identity, and UI behavior consistent across product applications.

## Development roles and lanes

| Machine | Lane | Mobile responsibility |
| --- | --- | --- |
| `minipc` | `worktree32` | Flutter/Dart and Android development, mobile API integration, emulator testing, and physical Android validation. |
| `macmini` | Dedicated iOS lanes | iOS development, Xcode builds, Simulator testing, and physical iPhone validation. |

Dedicated worktrees preserve the source, runtime configuration, and development context for each stream. Windows serves as a controller and optional graphical client, while Linux and the Mac mini run their respective mobile toolchains.

Git provides the source handoff between hosts. Builds and test results are tied to the application revision, while SDK paths, generated artifacts, caches, device state, and signing material are managed within their platform environments.

## Android infrastructure

The Linux baseline uses Flutter, its bundled Dart SDK, a compatible JDK, Android command-line tools, and hardware-accelerated emulation through KVM. Android Studio is optional. Toolchain upgrades are checked through builds and device validation.

One canonical Android Virtual Device is shared across the mobile suite. Independent application identities allow the products to coexist, making package, storage, native communication, and shared-session interactions visible during testing. Device access is coordinated, with each operation targeting the selected phone or emulator.

The emulator can run headlessly while still exposing rendered screenshots, UI hierarchy, input, installed-package metadata, and scoped logs. An on-demand Xfce remote desktop provides a visible emulator when interactive inspection helps. Quick Boot preserves the same virtual phone state between sessions.

A USB-connected Samsung phone supports physical-device validation. The installation helper selects the authorized phone, verifies that the APK belongs to the active checkout, and upgrades the installed application while preserving its data.

## iOS infrastructure

The Mac mini provides Xcode, Apple SDKs, Simulator runtimes, and native dependency integration for the shared Flutter workspace. Dedicated iOS worktrees keep Apple-specific project configuration and build activity organized alongside the common Dart code.

iOS validation uses both the Simulator and a physical iPhone. The Simulator supports a repeatable development loop, while iPhone testing exercises the installed application on real Apple hardware. Physical iPhone validation has been completed as part of the mobile development process.

## From build checks to user journeys

| Validation layer | Purpose |
| --- | --- |
| Formatting, analysis, unit and widget tests | Check source quality, service behavior, algorithms, and presentation logic. |
| Build and installation | Verify platform dependencies, generate application packages, and install on the selected target. |
| Emulator and Simulator journeys | Exercise controls, navigation, state transitions, and rendered application behavior. |
| Physical-device testing | Validate installed applications on Android and iPhone hardware. |

Flutter integration tests exercise application controls. Device tooling provides screenshots, UI state, installation details, and scoped logs. Mobile network journeys use selected HTTPS environments and normal system certificate trust, checking the response and error/retry behavior in the application.

## Repeatable application updates

Normal validation upgrades the installed app while preserving its data and then starts a fresh process. This checks retained preferences, session restoration, and application lifecycle behavior across updates. Integration-test cleanup is checked explicitly, and the normal application entry point is restored after a test build.

Build identities, screenshots, and test results connect each validation run to the application version and target device. This makes the development loop reproducible across the shared mobile foundation and the products built on it.

Continue with [mobile architecture](../platform/mobile-architecture.md) for shared packages and product boundaries, or [automated testing and quality](../platform/testing-and-quality.md) for the broader validation approach.
