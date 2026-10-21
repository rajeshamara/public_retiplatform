# AI and reusable agents

[Platform architecture](README.md) | [RetiPlatform home](../README.md)

RetiPlatform combines deterministic services with model-assisted interpretation. Reusable agents make those capabilities available to workflows, while model-provider adapters keep integration details out of product business logic.

## Two meanings of agent

A **workflow agent** is a focused adapter around a backend capability. It accepts typed input and context, calls services, and returns a result. Document requests, form generation, notifications, and HTTP acquisition can all be agents without invoking a language model.

A **model-driven agent** uses a model to choose among provided tools within an iterative interaction. The common service records the result, tool calls, iteration count, and available usage metadata. Its configurable iteration bound limits the number of tool rounds.

Keeping these responsibilities distinct allows a workflow to combine ordinary software and model-assisted work without making every step depend on model judgment.

## Implemented integration capabilities

| Capability | Implementation approach |
| --- | --- |
| Common model client | Provider adapters for OpenAI, Anthropic, Google Vertex AI, and xAI behind a shared client interface. |
| Configured model selection | Provider and model selection through service inputs and application configuration. |
| Prompt responses | Common request handling and typed response/usage models. |
| Tool use | Provider adapters translate tool definitions and return tool-call history with the final response. |
| Bounded interaction | Configurable iteration limits and request timeout inputs for supported service paths. |
| Retrieval-augmented responses | Embedding-based retrieval supplies relevant stored content as context for a model response. |
| Structured extraction | Document-processing services use model-assisted extraction within the application's data and review process. |

A common interface does not imply that every provider supports identical features or that each provider path has the same validation coverage. Embedding retrieval also has its own provider integration.

## From a capability to a workflow step

The reusable unit is the business service. An API router and a workflow agent can both call that service using the same input model. The service owns the business operation, persistence, and applicable authorization; the agent supplies the workflow context and returns the service result.

For example, a document-processing workflow can request missing evidence, wait for a response, invoke extraction, prepare a form deterministically, and create a human review task. The [workflow engine](workflow-engine.md) handles sequencing and waiting; the individual services handle their domain operations.

## Retrieval and information quality

Retrieval can bring relevant document content into a response, but it does not by itself guarantee that every statement is grounded in that content. The current response service can also use model knowledge when retrieved context lacks an answer.

For consequential business work, the architecture emphasizes explicit source ownership, deterministic validation, saved artifacts, and human review. Confidence-based escalation and broader policy-driven model routing are development directions whose availability must be established for each use case.

## Usage and execution visibility

Typed model results carry usage information where the provider supplies it. Workflow execution records separately retain agent status, input/output, timing, and errors. Together, these provide information for investigating model-assisted work within a larger process.

Usage metadata is distinct from a complete customer-billing or quota system. Likewise, recording a tool call does not establish that the business operation it requested succeeded; services and workflows retain their own completion evidence.

## Development approach

Known mappings, calculations, and permission checks stay in deterministic services. Models are used for tasks where language interpretation or extraction adds value. Product-specific variation is expressed through prompts, tool contracts, schemas, and workflow composition while shared infrastructure remains reusable.

This separation supports provider changes and new domain workflows without replacing the surrounding identity, document, persistence, and review mechanisms.
