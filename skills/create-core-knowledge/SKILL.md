---
name: create-core-knowledge
description: Scans any project repository and generates a "Source of Truth" documentation set in the core-knowledge folder, covering architecture, business logic, feature flags, deployment, and any cloud/serverless integrations.
version: 1.0.0
category: documentation
---

# Skill: generate-core-knowledge

<purpose>
Instructs the agent to perform a deep recursive analysis of the current repository and populate a `COREKNOWLEDGE_FOLDER` with a canonical set of technical documents that any future agent or developer can rely on as the definitive source of truth for the project.

Unless the user specifies otherwise, `COREKNOWLEDGE_FOLDER` = `.agents/core-knowledge` (relative to the workspace root).
</purpose>

<instructions>

## Phase 0 — Locate and Read Existing Knowledge

1. Resolve `COREKNOWLEDGE_FOLDER`:
   - If the user explicitly named a folder, use that path.
   - Otherwise default to `.agents/core-knowledge` at the workspace root.

2. Check whether `COREKNOWLEDGE_FOLDER` exists and list its contents.
   - If it contains any `.md`, `.txt`, or `.json` files, **read every one of them in full** before doing anything else.
   - Treat their content as authoritative context that must be preserved and extended — never contradicted or silently discarded.

3. If `COREKNOWLEDGE_FOLDER` does not exist yet, create it.

---

## Phase 1 — Project Reconnaissance

Perform a recursive scan of the repository. Collect enough evidence to answer each question below before writing a single document. Read files in parallel where possible.

### 1.1 Repository Layout
- What is the top-level folder structure?
- Is there a primary application subfolder (e.g. a framework project inside a monorepo)?
- What framework / runtime is in use (Node, Python, Java, …)? Check `package.json`, `pyproject.toml`, `pom.xml`, `go.mod`, etc.

### 1.2 Source Code Structure
- Map `src/` (or equivalent) top-level folders: api modules, services, utilities, types, middleware, plugins.
- Identify the entry point(s) of the application.

### 1.3 API / Endpoint Design
- Enumerate all API modules or route files.
- Identify any versioning strategy (URL prefix, header, file naming like `v2-*`).
- Note any intentional exceptions to the versioning rule (e.g. legacy endpoints kept for migration reasons).
- Document the folder/file naming convention for controllers, services, and routes.

### 1.4 Integration / External Services Layer
- Find HTTP client code that calls third-party or internal APIs.
- Document the data flow: external API response → mapper/transformer → typed internal model → controller response.
- Identify authentication mechanisms (API keys, Basic Auth, OAuth, JWT, etc.).

### 1.5 Configuration & Feature Management
- Read base config files and all environment-specific config overrides.
- Find all environment variables (`.env`, `config/*.ts`, `*.yml`, `*.toml`, …).
- Identify boolean feature flags and what behaviour they gate.
- Document how multi-tenant or multi-client config is split (if applicable).

### 1.6 Authentication & Authorization
- Identify the auth strategy (JWT, session, API key, Cognito, Auth0, …).
- Document how the identity is populated into request context and consumed downstream.
- Note any role or policy-based access control patterns.

### 1.7 Database & Migrations
- Identify the database driver and ORM/query-builder.
- Locate migration files and document the migration strategy.
- Document model/schema conventions.

### 1.8 CI/CD Pipeline
- Read pipeline config files (`.gitlab-ci.yml`, `.github/workflows/*.yml`, `Jenkinsfile`, …).
- Map stages, jobs, trigger conditions (branch, tag, manual), and deployment targets.

### 1.9 Cloud / Serverless Functions
- Check for serverless function folders (AWS Lambda, GCP Cloud Functions, Azure Functions, …).
- For each function, identify: trigger type, purpose, runtime, and relationship to the main application.

### 1.10 Tests
- Locate test folders and identify the testing framework.
- Note the split between unit and integration tests if present.

---

## Phase 2 — Document Generation

Create or update the following files inside `COREKNOWLEDGE_FOLDER`. If a file already exists, **extend or correct it** — do not overwrite content that is still accurate.

### `architecture.md`
Must include:
- **Directory map**: An ASCII tree of the `src/` folder (or equivalent) with one-line descriptions per node.
- **Data flow diagram**: ASCII diagram showing how a request travels from client → auth/middleware → controller → external service client → mapper → response.
- **API versioning convention**: Explain the URL and file-naming patterns. Explicitly call out any intentional exceptions (legacy endpoints, migration holdouts, etc.).
- **Authentication flow**: How tokens/credentials are validated and how user identity is injected into request context.
- **Coding conventions checklist**: Step-by-step rules for creating a new endpoint, including: folder structure, file naming, auth policy usage, input validation library, error response helpers, external call discipline, and type conventions.
- **Content-type / model conventions** (if the framework has a CMS or ORM layer).

### `business-idea.md`
Must include:
- **Product overview**: What the system does and who uses it.
- **Client / tenant distinction**: If multi-tenant, document each tenant's scope, commodity types, feature access, and any dedicated API modules.
- **Migration status**: If an ongoing migration is in progress, document which tenants/features are ahead or behind and what gates the switch (feature flags, branches, etc.).
- **Key initiatives**: Document any major in-progress feature initiatives (e.g. a billing project, a new data model, a "Huge-clients" performance initiative), their status, and how they affect code conventions.
- **CMS / editorial role**: If the backend also manages editorial content, describe what content types drive frontend behaviour.

### `feature-flags.md`
Must include:
- A table or section for **every** environment variable and feature flag found, with: env var name, config key path, type, default value, what it gates, and which files reference it.
- Separately document any **runtime toggles** (flags stored in a database or admin panel rather than env vars), including who can change them and the effect on behaviour.

### `deployment.md`
Must include:
- **Infrastructure overview table**: hosting platform, region, auth service, logging, storage, CDN, database.
- **Environment inventory**: every named environment (dev, uat, staging, prod, etc.) with its URL, hosting resource name, and the tenant/client it serves.
- **Local development setup**: prerequisites, commands to install, build, and start the server for each company/environment variant.
- **CI/CD pipeline walkthrough**: stages, per-job trigger rules and approval requirements, artefact flow.
- **Deployment procedures** per environment tier (automatic vs manual, branch/tag conventions).
- **Troubleshooting**: document any known recurring deploy issues and their resolution steps.
- **Branching model**: document the Git branching strategy and how it maps to environments.

### `<integration-name>.md` (one file per significant external integration, if present)
Examples: `aws-lambdas.md`, `stripe-integration.md`, `salesforce-integration.md`

Each integration file must include:
- Inventory table: every function/endpoint with its trigger/event type, client/tenant scope, and environment scope.
- Detailed logic description per item.
- Client or tenant differences within the same integration.
- SDK/runtime version history if multiple versions coexist.
- Deployment/update procedure for that integration.
- How the integration relates to backend request processing.

---

## Phase 3 — Source-of-Truth Oath

Append the following block verbatim at the top of `architecture.md` (immediately after the H1 title):

```
> **Source-of-Truth oath**: Before generating any code, reference the files in this
> core-knowledge folder to ensure naming, file placement, and architectural patterns
> match the project's established standards — specifically respecting any
> versioning distinctions and client/tenant mapping logic documented here.
```

</instructions>

<constraints>
- Do NOT overwrite content in existing knowledge files that is still accurate. Extend or correct only.
- Do NOT fabricate details that are not present in the codebase. If something is unclear, say so explicitly in the document.
- Do NOT create additional markdown files outside `COREKNOWLEDGE_FOLDER` unless the user requests it.
- Every document must be written for a future agent reader: be precise, use exact file paths, code identifiers, and command strings rather than vague prose.
- If a section of a document is not applicable to this project (e.g. no serverless functions), omit that section rather than leaving a placeholder.
</constraints>

<output_format>
After all files are written, reply to the user with a brief confirmation listing:
- Each file created or updated (as a workspace-relative link)
- One sentence describing what was added or changed in each file
</output_format>
