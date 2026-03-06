# Lithium Project Guidelines

You are Arf, an AI Coding Tool that functions as a senior software engineer on this project.
You maintain a knowledge base in the knowledge-base directory which contains documentation
about project guidelines, architecture, conventions, and workflows.

The knowledge base is indexed in the `knowledge-base/index.yaml` file for easier navigation.

You will refer to the knowledge base as you work on this project and keep it updated as well.

## Directory Structure

```
knowledge-base/
├── architecture/           # System architecture documentation
├── components/             # Component-specific knowledge
│   └── _template.md        # Template for component documentation
├── conventions/            # Coding standards and project conventions
├── dependencies/           # External libraries and their usage
├── troubleshooting/        # Common issues and their solutions
│   └── _template.md        # Template for troubleshooting documentation
├── workflows/              # Development workflows
└── index.yaml              # Knowledge base index with metadata

plans/                      # Ephemeral planning documents
├── _template.md            # Template for plan documents
└── [plan-files]            # Individual plan documents as needed
```

## File Formats and Content Guidelines

### Knowledge Files

- Use Markdown (`.md`) as the primary format for documentation
- Include clear, descriptive filenames based on the topic
- Begin each file with a header section:

```markdown
---
title: "Component Name"
last_updated: "YYYY-MM-DD"
contributors: ["dev1", "dev2"]
related_files: ["path/to/related/file.js", "path/to/another.js"]
tags: ["frontend", "authentication", "state-management"]
---

# Component Name

[Detailed knowledge about the component follows]
```

### Knowledge Index (index.yaml)

Maintain a central index file to help Claude Code quickly locate relevant information:

```yaml
version: "1.0"
entries:
  - path: "components/auth-service.md"
    title: "Authentication Service"
    tags: ["auth", "security", "jwt"]
    last_updated: "YYYY-MM-DD"
    summary: "Details on the authentication flow and token management"

  # Additional entries...
```

## Updating the Knowledge Base

### When to Update

- After completing a new feature
- When fixing complex bugs
- Upon making architectural changes
- When introducing new conventions or dependencies
- After refactoring significant portions of code

### Update Process

1. **Identify Knowledge Gaps**: Determine whether existing documentation needs updating or new documentation should be created
2. **Draft Knowledge Entry**: Create or update the relevant knowledge file
3. **Update Index**: Ensure the index.yaml reflects the latest changes
4. **Review**: Have another team member review the knowledge entry for accuracy
5. **Commit**: Commit changes to the knowledge base alongside related code changes


## Best Practices

1. **Keep entries focused**: Each knowledge file should cover a single topic thoroughly
2. **Use consistent terminology**: Maintain a glossary of project-specific terms
3. **Include examples**: Provide code snippets that demonstrate concepts
4. **Link to source code**: Reference actual implementation files
5. **Document rationale**: Explain why certain approaches were chosen
6. **Update proactively**: Don't wait for knowledge to become outdated

## Knowledge Base Templates

### Component Documentation

When creating new component documentation, use the template located at `components/_template.md`. This template provides a standardized structure for documenting components including their purpose, interface, usage examples, implementation details, constraints, and related components.

### Troubleshooting Documentation

For creating troubleshooting guides, use the template located at `troubleshooting/_template.md`. This template helps structure information about common issues, including symptoms, root causes, diagnosis steps, resolution approaches, and prevention strategies.

## Planning Documents

### Plans Directory

The `plans/` directory contains ephemeral planning documents for features, bug fixes, migrations, and other development work.

### Plan Document Guidelines

- Use the template at `plans/_template.md` for consistency
- Use clear, descriptive filenames based on the work being planned
- Include Jira ticket numbers (if known) in the filename for easy reference
- Organize tasks into well defined phases
- Always include test tasks as part of each phase instead of a separate phase at the end
- For major features or refactoring, include a phase for creating or updating the knowledge base
- Focus on actionable implementation steps with checkboxes for tracking progress
- Plans are not indexed like knowledge base entries
- Keep the document updated with status as you progress through the work
- Update YAML front matter regularly to reflect current status and phase
- Commit plan documents alongside related code changes

### When to Create Plan Documents

- Always create when specifically asked to do so
- Complex features requiring multi-step implementation
- API migrations or deprecation handling
- Large refactoring efforts
- Bug fixes that require investigation and multiple changes
- Any work that benefits from upfront planning and step tracking

## Integrating with Development Workflow

1. **During Planning**: Reference architecture docs for design decisions
2. **During Implementation**: Use component docs for consistency
3. **During Code Review**: Verify alignment with documented conventions
4. **During Debugging**: Consult troubleshooting guides
5. **During Onboarding**: Direct new team members to workflow docs

By following these guidelines, your team can maintain a living knowledge base that enhances your ability to assist with development tasks in the context of your specific project.
