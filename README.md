# core-knowledge
[![skills.sh](https://skills.sh/b/n4sch/core-knowledge)](https://skills.sh/n4sch/core-knowledge)

A collection of AI agent skills for documenting and developing software projects with GitHub Copilot.

## Skills

### `create-core-knowledge`
Scans any project repository and generates a "Source of Truth" documentation set in a `.agents/core-knowledge` folder, covering architecture, business logic, feature flags, deployment, and cloud/serverless integrations.

### `add-feature`
Implements a codebase feature based on the existing `core-knowledge` documentation and returns a ready-to-use PR description.

## Installation

Install the skills into your agent configuration using the [skills.sh](https://skills.sh) CLI:

```bash
npx skills add n4sch/core-knowledge --agent universal
```

## Usage

Once installed, invoke the skills in GitHub Copilot Chat:

- **Document your project**: Ask Copilot to use the `create-core-knowledge` skill to scan and document your repository.
- **Add a feature**: Ask Copilot to use the `add-feature` skill to implement a task based on the generated documentation.

### Updating Installed Skills

To pull down the latest security fixes, constraints, or feature updates from this repository, run:

```bash
npx skills update
```