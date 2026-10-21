# Custom workflow engine

[Platform overview](README.md) | [RetiPlatform home](../README.md)

The workflow engine is a custom Python implementation for persistent business processes. It separates **what a process does** from **how each capability is implemented**: a definition composes reusable agents, an executor advances the process, backend services perform the business operations, and persisted records describe the work completed or still waiting.

An agent is a reusable capability adapter; it does not have to invoke an AI model. Document requests, deterministic form generation, notifications, and HTTP data acquisition can participate alongside model-assisted work. This lets a process use ordinary application logic where the answer is known and AI where interpretation adds value.

The following diagram is a conceptual view of the engine, rather than a deployment map:

```mermaid
flowchart TD
    A[Workflow definition and runtime input] --> B[Validate and persist workflow run]
    B --> C[Dispatch to workflow executor]
    C --> D{Next step}
    D --> E[Service-backed agent]
    D --> F[Nested or parallel child workflows]
    D --> G[Human task or external-event wait]
    E --> H[Persist output and execution state]
    F --> H
    G --> I[Validate response or event]
    I --> H
    H --> J{More work?}
    J -->|Yes| D
    J -->|No| K[Completion and execution history]
```

## Definitions, data, and composition

| Capability | Implemented behavior |
| --- | --- |
| Declarative definitions | JSON definitions describe a workflow's name, description, version metadata, inputs, tasks, and output mapping. Registered definitions can be loaded from repository assets or object storage. |
| Input contracts | Required-field and basic top-level type checks reject unsuitable input before execution. |
| Context and parameter binding | Steps consume original inputs, earlier step results, and the current item within a loop; outputs become available to later work. |
| Sequential execution | Ordered steps compose a process from smaller capabilities. |
| Conditional routing | If/else branches and exact-match switch branches, including defaults, select the next work from runtime data. |
| Collection processing | Serial loops repeat a task sequence over a collection. |
| Concurrent execution | Parallel task blocks run independent work concurrently. |
| Nested workflows | A workflow can call another reusable workflow with explicit inputs and mapped results, retaining parent/child relationships. |
| Durable parallel children | Child workflow identities and parent join state are persisted. Children can pause independently; the parent continues when all children complete. |
| Reusable service adapters | Agents expose focused business capabilities through the service layer, allowing the same underlying behavior to serve workflows and APIs. |

Definitions express a reusable process; people, organizations, and other run-specific facts enter as runtime data. This supports different business scenarios without embedding a particular client's information in a workflow template.

## Human work and external events

Long-running processes need explicit waiting states. A document request or attorney review should remain meaningful after the user closes a browser.

- **Persistent human tasks:** a checkpoint can carry a role, an optional named assignee, an action, a UI form, instructions, a due date, and input/output data.
- **Controlled completion:** task completion checks the assigned reviewer, actionable state, and expected output. Required approval checkpoints cannot simply be skipped.
- **Artifact-specific review:** selected form and package review services bind a decision to the saved artifact being reviewed, keeping the approval connected to that version of the work.
- **External-event waits:** a process can wait for a specified event and check its type, source, matter, actor, owner, and required payload values before continuing.
- **Duplicate-event handling:** event acceptance and rejection are recorded, and repeated deliveries are detected, helping prevent a repeated notification from advancing the same wait twice.
- **Workflow-driven communication:** reusable email and document-request capabilities support template selection, conditional send/skip decisions, and delivery results. Document requests include suppression of unchanged requests and an explicit resend path.

## Execution, persistence, and recovery

The API persists a workflow instance before dispatching work to a separate executor. Dispatch adapters support local HTTP execution and Google Cloud Tasks. The executor has concurrency limits and reports active and waiting capacity.

Persistent workflow and task records carry inputs, outputs, status, timestamps, errors, execution identity, parent/root tracking, and relevant company, product, and matter context. Atomic execution claims and state checks guard against concurrent delivery, already-running instances, and inappropriate advancement of paused or terminal work.

Recovery is explicit and capability-specific. Implemented paths include retrying an initial dispatch, recovering selected children that have not started, and assessing supported deterministic preparation retries against retained attempt, version, and content-hash evidence. These paths preserve the workflow identity so recovery remains connected to the original run.

## Visual exploration and monitoring

The application provides a workflow explorer and execution monitor:

- Searchable, paginated workflow definitions with expandable nested task trees.
- Condition and branch inspection, per-node properties, input details, and a JSON view.
- Workflow start and refresh controls.
- Workflow and agent execution lists with status, timestamps, errors, and final messages.
- Root-only or all-instance views, tracking-based drilldown, hierarchical execution trees, and graph views.
- Matter-specific workflow queries that connect a business record to its running processes.

The active interface emphasizes inspection, starting work, and understanding execution. A complete visual authoring and publishing experience is a separate development direction.

## Applying the engine to legal work

An illustrative process is: collect matter inputs, request missing documents, wait for the client's response, extract relevant information, prepare a form, obtain attorney review, and continue to the next approved step. The engine provides the composition and waiting mechanisms; domain services define the documents, data requirements, permissions, and review rules.

Concrete reusable capabilities already exist for onboarding, document requests, form generation, and notifications. Broader immigration workflows continue to be integrated and validated. A workflow definition by itself is not evidence that every legal step or external filing is automated.

**Current engine boundaries:** durable parallel child workflows use a wait-for-all join; collection loops run serially. Recovery supports defined continuation paths, rather than arbitrary replay from any failure. Version metadata does not imply immutable version pinning for every run. A general recurring scheduler, configurable automatic retry/backoff policy, and complete no-code publishing are not claimed as delivered capabilities.
