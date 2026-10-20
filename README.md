# RetiLaw

**A legal practice platform in development, focused on immigration workflows.**

Built by [Rajesh Amara](https://github.com/rajeshamara), founder and hands-on solutions architect.

RetiLaw brings matter information, documents, form preparation, and team workflows into a connected application. Its current product focus is immigration practice, including employment-based matters and the preparation work surrounding Department of Labor forms and the FLAG portal.

This repository is a public project overview. The application implementation is maintained privately.

## The problem

Immigration work involves information moving between client records, matter screens, supporting documents, forms, and external portals. Re-entering the same information creates additional work and makes it harder to keep a matter consistent as details change.

RetiLaw is being built around a shared workflow: organize matter data, use that information to prepare documents and forms, and keep review connected to the underlying records. Attorneys and firm staff remain involved in checking information and deciding when work is ready to proceed.

## Implemented components

The development codebase includes the following engineering work. These components continue to be integrated, refined, and validated as the product develops.

- **Authenticated application and matter workflows:** application access, role-aware functionality, backend APIs, and attorney-facing matter screens.
- **Document and form processing:** components for document processing, structured information extraction, and form preparation, with human review included in the workflow.
- **Agent workflows:** coordination between specialized agents and application services to support document and form tasks.
- **Contextual information access:** retrieval-augmented generation and chatbot integrations for working with domain information.
- **Access controls:** authentication, authorization, roles, and multifactor authentication as part of the application foundation.
- **Mobile integration:** mobile application features connected to backend APIs, authentication, and business workflows.
- **Automated validation:** Playwright automation across authenticated user profiles and roles to exercise application workflows and access behavior.

## Work in progress

Current product development connects these components into broader immigration workflows: matter organization, ETA form preparation using structured data, document generation, and controlled assistance with FLAG portal work. The portal-assistance direction emphasizes user review and controlled transfer of information.

Development also includes refining the relationship between web and mobile experiences, strengthening integration coverage, and expanding validation across user roles and workflow states. Feature scope continues to evolve as these workflows are implemented and tested.

## Architecture and technology

| Area | Technology or approach |
| --- | --- |
| Web application | React and Vite |
| Backend services | Python and FastAPI |
| Data | PostgreSQL |
| Cloud platform | Google Cloud Platform |
| AI integration | Agent workflows, structured extraction, RAG, and chatbot integration |
| Application validation | Playwright and role-based workflow testing |

### Engineering decisions

**Reuse structured information.** Matter data provides a common foundation for documents and forms. This keeps data preparation connected to the application workflow and reduces the need to build each document task as a separate process.

**Keep AI work reviewable.** AI capabilities participate in defined application workflows, with human review included in document and form preparation. The architecture connects model-assisted tasks with application services and permissions.

**Make firm and user boundaries explicit.** Access controls are part of application design and validation. Role-specific tests help examine whether users can reach the workflows appropriate to them.

**Use APIs across application surfaces.** Backend services support web and mobile capabilities, allowing business workflows and authentication to be integrated across interfaces.

## My role

I lead product direction, solution architecture, implementation, integration, and technical validation. This includes translating legal workflow needs into application behavior, connecting frontend and backend capabilities, and developing the AI and automation components.

I use AI-assisted engineering tools for implementation, refactoring, debugging, and test automation while retaining responsibility for architecture, code review, and validation. RetiLaw demonstrates my approach to building a domain-focused product across user experience, services, data, access controls, and applied AI.

## Status

**RetiLaw is under active development.** This overview describes the technical work and product direction; the complete platform remains a work in progress. Public material will be updated as capabilities are validated and the product develops.

For professional background and other public work, visit [my GitHub profile](https://github.com/rajeshamara).
