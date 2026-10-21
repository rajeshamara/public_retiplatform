# RetiBot

[RetiPlatform home](../README.md) | [Platform architecture](README.md) | [Intent-to-command architecture](retibot-intent-execution.md)

**A shared conversational interface for product help, general knowledge, and business-command proposals.**

RetiBot brings three kinds of interaction into one chat experience: explaining the product, answering general questions, and proposing a supported application action. Its command architecture connects natural-language intent to application-owned capabilities through a saved plan and a separate execution request.

RetiBot belongs to the shared RetiPlatform web foundation. Products using that application shell reuse the same chatbot components and service path. This lets the platform develop conversation behavior once while retaining product-specific navigation and business boundaries.

> **Current development scope:** the shared chat interface, knowledge-answering path, intent analysis, action-plan persistence, and confirmation controls are present in the source. The command path, including catalog loading and workflow dispatch, needs integration fixes and further authorization work before it can be described as a complete end-to-end capability. The architecture below distinguishes those existing building blocks from the intended execution contract.

## One chatbot, three behaviors

| Behavior | Purpose | How it is handled |
| --- | --- | --- |
| **Business commands** | Turn a supported request into a proposed application operation. | Classify against a configured intent catalog, prepare a saved action plan, request confirmation, and hand off to an application-owned handler. The execution integration remains in development. |
| **Product help** | Answer questions using available product information. | Retrieve relevant document passages and supply that context to the model. Coverage depends on the indexed information. |
| **General knowledge** | Answer ordinary questions beyond the retrieved product material. | Allow a model-generated response when the available context does not answer the question. |

These are behaviors behind one input field. Users do not need to switch among three separate chatbots or select a command/help/general mode.

The answer path and the action path have different outcomes. A help response returns text. A recognized command is designed to return a proposal with an action identifier, requiring a separate confirmation step before dispatch. A general answer does not itself become an application operation.

## Shared across the product application

The shared web shell supplies a floating RetiBot widget, and a dedicated chat page reuses the same conversation state and service. The widget supports opening, closing, resizing, and a docked presentation. Loading and error responses appear within the conversation.

The same implementation is reused in the platform's product shell rather than maintained as separate RetiLaw, RetiCRM, RetiSign, and RetiAuth chatbots. Dedicated-page navigation and layout availability vary by product configuration. Reuse of the component does not mean every product already exposes the same commands or has passed the same acceptance checks.

This page describes the shared **web** assistant. It does not establish RetiBot command support in every native mobile app.

## Product help and general answers

RetiBot uses embedding-based retrieval to find relevant stored passages. Those passages become context for intent analysis and knowledge answering. The answering instructions prefer relevant context, then allow general model knowledge when the context lacks an answer.

This supports a useful progression within one interface: ask what a feature does, ask a related general question, or express a request that may correspond to a business action.

Current retrieval uses an existing product-information corpus. A shared chatbot component does not automatically provide a separate, permission-filtered knowledge collection for every product and company. Corpus expansion and access-aware retrieval require their own implementation and validation.

The current response contract also does not distinguish a retrieved answer from a general-knowledge answer with guaranteed citations. General responses should not be interpreted as proof of current product behavior, private-record access, or completed work.

## Intent-driven command design

The command architecture gives natural language a controlled role: identify a possible business intent. Application code retains the responsibility for deciding whether that intent has a supported handler and whether the requested operation may run.

The intended interaction is:

**Request → recognize an allowed intent → prepare a plan → confirm → validate and dispatch → report the result.**

The existing reference handler concerns account-activation emails. Additional catalog entries describe possible operations, but a catalog entry alone does not implement a command. Each command needs its own handler, input contract, authorization, execution integration, and validation.

The [intent-to-command architecture](retibot-intent-execution.md) explains the stages, data boundaries, workflow handoff, and current integration status in detail.

## Guardrails and their responsibilities

The implementation contains several controls around action proposals:

- **Catalog-constrained classification:** the classifier is instructed to choose from known intents, and application code checks the returned label against the loaded catalog.
- **Structured results:** the model returns a structured intent, parameters, and message; parsing failures take an answer fallback rather than directly dispatching a command.
- **Separate proposal and execution:** a recognized action is saved before the interface offers **Confirm & Execute**.
- **Stored-plan execution:** confirmation sends the action identifier, and the service reloads the saved plan instead of accepting a new browser-supplied command body.
- **Pending-state check:** the execution service checks that the saved action is still pending before attempting its handler.
- **Application-owned operations:** the reference integration calls a named workflow service; model text is not evaluated as shell commands, SQL, or generated application code.

These controls have different purposes. Catalog membership limits recognition, confirmation asks for an explicit UI decision, and authorization determines who may perform the operation. Confirmation and prompt instructions do not substitute for server-side permission checks.

The present command path still needs integration repair, authenticated plan ownership, product/resource authorization, command-specific parameter validation, and stronger execution-state handling. The detailed architecture page records these as requirements rather than claiming complete enforcement.

## Conversation and confirmation scope

The floating widget and full chat page share an in-memory transcript. The current query sends the latest prompt; it does not forward the entire transcript or the active business record as model context. Saved widget preferences control presentation, while action plans use separate server persistence.

**Confirm & Execute** is rendered in the floating widget and dedicated chat page. The current docked presentation does not expose that action control. The interface shows a proposed operation, but does not yet provide a full editable review of effective targets and parameters.

These distinctions matter when extending the assistant: conversational continuity, business-record context, and durable action history each need an explicit contract.

## What the architecture adds

RetiBot's architectural contribution is the connection between conversation and application behavior. Retrieval supports product explanation; model knowledge supports broader questions; a structured action path gives supported requests a route toward existing business services and workflows.

The shared interface, reusable service boundaries, saved proposals, and explicit confirmation step provide a foundation for that progression. Completing and validating the execution boundary is the next requirement for publishing stronger command-automation claims.

Related chapters: [AI and reusable agents](ai-and-agents.md), [custom workflow engine](workflow-engine.md), and [identity and tenancy](identity-and-tenancy.md).
