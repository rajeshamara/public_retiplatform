# AI-assisted development

[Infrastructure overview](README.md) | [RetiPlatform home](../README.md) | [Development environment](development-environment.md) | [Testing and quality](../platform/testing-and-quality.md)

RetiPlatform uses **Codex and Claude Code** for implementation, investigation, review, testing, and documentation. Their work is organized around explicit repository rules, assigned worktree lanes, reusable capabilities, and observable acceptance criteria. Dedicated Claude Code worktrees participate in the same engineering process as Codex-owned lanes.

This is the development workflow. The [platform's runtime AI and reusable agents](../platform/ai-and-agents.md) are a separate part of the product architecture. A coding assistant's ability to change the repository does not grant a product assistant permission to act on customer data.

## Architecture before implementation

Each assignment starts with a checkout check and a defined reading scope. The repository maintains a mandatory architecture core, an indexed set of topic guides, and focused playbooks for common changes such as services, routers, models, UI components, and reusable agents. The Claude Code entry point refers back to the same shared rules.

The startup process requires the agent to:

1. Confirm the assigned repository root, attached branch, and starting commit.
2. Read the common architecture and select the full task-specific guidance and prerequisites.
3. Identify the existing implementation or reference behavior before proposing another path.
4. Register the assignment and state the applicable constraints and validation mode.
5. Classify the change as shared platform behavior, product composition, domain configuration, or genuinely unique functionality.

This supports a consistent development model across assistants. Routers coordinate HTTP concerns, services own business logic, and database access remains behind approved helpers. Reusable workflow agents call those services rather than maintaining a second implementation. A shared change must include representative existing consumers in its validation scope.

## Parallel work with explicit ownership

Permanent worktrees let product and feature development proceed concurrently. Each lane has its own branch, working files, service-port assignments, and validation evidence. Assignments should be divided by capability or file ownership to reduce accidental overlap.

The operating rules distinguish **publishing a lane**, **declaring it ready**, and **integrating it into main**. A pushed branch may still contain work in progress. Integration needs a validated tip and explicit release from its owner, followed by the shared comparison and conflict checks described in the [development environment](development-environment.md).

The process is intended to make contributions inspectable regardless of which assistant produced them. It does not assume that generated code is correct, that parallel agents are independent reviewers of every change, or that passing a merge check establishes product correctness.

## Task records, worklogs, and handovers

The repository provides Windows and Linux/macOS reporting commands backed by a central task record. A task retains its identifier, reporter, development host, worktree, description, start time, and status across retries and handovers. Completion is reported after the assignment and its required validation finish.

Application changes also have lane-scoped worklogs describing the problem, cause, solution, and affected files. The check-in process can link the worklog from the commit message. Handover documents capture one executable task contract: the goal, authoritative files, exact current state, completed checks, remaining work, and blockers.

These records serve different purposes. A task status communicates reported progress; a worklog explains a change; a handover preserves continuation context; test evidence supports a specific behavioral claim. None substitutes for the others or for independent review.

## New-model onboarding

Adopting a new model involves more than installing an updated coding client. The Codex runbook separates the layers that can become inconsistent across the Windows desktop and remote development hosts.

| Gate | Evidence required |
| --- | --- |
| Supported target | Confirm the intended model, account availability, supported client release, and installation method. |
| Installed executable | Identify the executable actually selected by the host and verify its installed version. |
| Running backend | Verify the app-server version in use; check any separately installed managed package as applicable. |
| Live model catalog | Confirm that the running backend exposes the intended model and supported settings. |
| Desktop selection | Confirm that the intended host's model picker displays and selects the model. |
| Successful request | Complete a small request and record the selected runtime/model metadata. |

A model appearing on one host does not prove availability on every other host. The repository includes a compact version checker, a controlled remote updater, and a live catalog probe. Their results distinguish installed and running versions; backend replacement checks for active work and fails visibly when state cannot be established.

The runbook preserves the host's existing installation method and treats a disruptive backend restart as a separate, controlled action. A catalog check is read-only and is not an inference test. OpenAI's [app-server model discovery documentation](https://learn.chatgpt.com/docs/app-server#list-models-modellist) likewise directs clients to use the returned model capabilities rather than assume a fixed catalog.

Recorded rollout evidence is scoped to the gates actually exercised. For example, a September 2026 repair recorded matching installed/running versions and a visible target model on the checked hosts, while desktop selection and inference were not separately revalidated after that repair. Historical versions are incident evidence, not permanent upgrade targets.

## Agent proof of work

A separate repository exercise checks whether an agent or model can follow the project's real operating instructions. It requires the agent to discover the correct RetiLaw browser setup, enter the authenticated attorney experience, open an existing matter, and save a screenshot of the resulting matter screen in its agent/model evidence folder.

The exercise is deliberately bounded: use the existing tooling, stay in the assigned worktree, and make no repository changes beyond the permitted evidence folder and screenshot. If a setup or policy prerequisite prevents completion, the agent must report that blocker instead of modifying the application to make the demonstration pass. Service control still follows the host's operating rules.

This is an onboarding capability check for instruction following, tool use, authentication setup, navigation, and evidence capture. Its pass condition is the demonstrated journey. It is not a model benchmark, a full regression run, or a statement that every available assistant has passed. Private screenshots and test-account information from the exercise are not automatically published.

## Proof before completion

Acceptance depends on the changed behavior. The engineering policy requires more than a successful build when the user-visible contract spans multiple layers:

| Change surface | Required kind of evidence |
| --- | --- |
| Backend or shared logic | Focused behavior checks and regression coverage for affected consumers. |
| Browser experience | The actual authenticated role and page journey, including relevant navigation and persistence behavior. |
| Saved forms and documents | Correct stored data, Save/reload behavior, and inspection of the generated or downloaded artifact where applicable. |
| Native mobile behavior | The actual app on its required emulator or device, with build identity and case-level outcomes. |
| Shared platform capability | The requested new behavior and representative existing consumers of that capability. |

Formal quality-gate cases live in a registered test catalog with canonical runners. Exploratory probes and raw observations remain separate until their contracts, assertions, and execution are stable enough for promotion. The [testing chapter](../platform/testing-and-quality.md) describes the automation, and [mobile validation](mobile-validation.md) records the distinct emulator, Simulator, and physical-device evidence.

The completion policy is to state exactly what was implemented, what was exercised, and what remains unverified. These are engineering controls and recorded practices; they do not constitute a claim that every historical change met every gate or that the platform is production-certified.
