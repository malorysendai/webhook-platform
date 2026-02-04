# TOOLS.md - Available CLI Tools & Programs

## Agent Communication

- `agents_list` - Discover available agents in the system
- `sessions_send` - Send messages to other agents
- `sessions_spawn` - Spawn sub-agents for async tasks

To see other agents, use `agents_list`. To communicate with another agent, use `sessions_send` with their agent ID.

## Project Management

- `gh` - GitHub CLI for issues, PRs, and project boards
- `linear` - Linear CLI for ticket management , use env LINEAR_TOKEN_MALORY for authentication

## Version Control

- `git` - Version control for specs and documentation
  - Creating branches for new specs
  - Creating PRs for review
  - Checking spec status

## Data & Analysis

- `jq` - For analyzing project metrics and report data

## Common Operations

### Create New Spec Branch

```bash
# Tier 1: Project Spec
git checkout -b project-{NNN}-{title}
mkdir project-{NNN}-{title}

# Tier 2: Feature Spec
git checkout -b feature-{NNN}-{title}

# Tier 3: Task Spec
git checkout -b task-{NNN}-{title}
```

### Create PR for Review

```bash
gh pr create --title "Spec: {title}" --body "## Summary\n{description}"
```

### Check Linear Status

```bash
# List assigned issues
linear issue list --assignee @me

# List milestone progress
linear milestone list --project {project}
```

---

Use these tools to keep the mission on track. And for heaven's sake, keep it orderly.
