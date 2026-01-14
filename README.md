# Claude Code Multi-Agent Setup

A lean, manual orchestration setup for software development with Claude Code agents.

## Philosophy

1. **Agents are LEAN** - Define WHO, not HOW (Claude already knows how)
2. **Knowledge-base is for EVERYONE** - Human + AI readable project docs
3. **Handoffs are for AGENTS** - Internal communication via files
4. **Manual orchestration** - You control the workflow, agents focus on their role
5. **Review at key points** - Design review + Test review catch issues early and late
6. **Agile iteration** - Small, integrated changes through the workflow beats big batch work

## Workflow

```
┌──────────────────────────────────────────────────────────────────┐
│                                                                  │
│   manager ──► architect ──► REVIEWER ──► developer ──► tester   │
│                               │                          │      │
│                          design review              REVIEWER    │
│                                                          │      │
│                                                    test review  │
│                                                          │      │
│                                                       devops    │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

## Directory Structure

```
~/.claude/agents/              # User-level agents (all projects)
├── manager.md                 # Requirements & task breakdown
├── architect.md               # System design (mermaid + markdown)
├── developer.md               # Implementation
├── tester.md                  # Testing & coverage
├── reviewer.md                # Design review + Test review
├── devops.md                  # CI/CD & deployment
├── simple-dev.md              # Quick fixes & small changes (bypasses workflow)
└── knowledge-base.md          # Generates & maintains architectural documentation

project/
├── CLAUDE.md                  # Brief overview, points to knowledge-base
├── knowledge-base/            # Human + AI documentation (can be submodule)
│   ├── INDEX.md               # Main entry point - what's where
│   ├── overview/              # Architecture summaries with embedded dependency graphs
│   │   └── architecture-summary.md
│   ├── modules/               # Per-module docs with embedded class diagrams
│   │   ├── <module-name>.md   # Each module: description + class diagram
│   │   └── ...
│   ├── architecture/          # Completed designs (archived from handoffs)
│   ├── features/              # Completed feature docs (archived from handoffs)
│   └── decisions/             # ADRs (architectural decision records)
└── .claude/
    ├── handoffs/              # Active work-in-progress communication
    │   ├── requirements.md    # manager → architect
    │   ├── design.md          # architect → reviewer → developer
    │   ├── review-design.md   # reviewer feedback on design
    │   ├── test-results.md    # tester → reviewer
    │   └── review-tests.md    # reviewer feedback on tests
    └── skills/                # Project-specific skills
        ├── python_kb/         # Python KB tools (copy from template if needed)
        └── kotlin_kb/         # Kotlin KB tools (copy from template if needed)
```

## Installation

### 1. Install agents (one-time)
```bash
mkdir -p ~/.claude/agents
cp user-level/agents/*.md ~/.claude/agents/
```

### 2. Initialize a project
```bash
# In your project root
mkdir -p knowledge-base/{overview,modules,architecture,features,decisions} .claude/{handoffs,skills}

# Copy templates
cp project-template/CLAUDE.md ./CLAUDE.md
cp project-template/knowledge-base/INDEX.md ./knowledge-base/
cp project-template/knowledge-base/decisions/ADR-TEMPLATE.md ./knowledge-base/decisions/
cp project-template/.claude/handoffs/*.md ./.claude/handoffs/

# Edit INDEX.md to match your project
```

## Usage

### Manual Workflow Example

```bash
# 1. Start with requirements
"Use the manager agent to break down the login feature"
# → Manager writes to .claude/handoffs/requirements.md

# 2. Design
"Use the architect agent to design based on the requirements"
# → Architect reads requirements, writes to .claude/handoffs/design.md

# 3. Design Review
"Use the reviewer agent to review the design"
# → Reviewer reads design, writes to .claude/handoffs/review-design.md
# → If NEEDS CHANGES: architect fixes, re-review
# → If APPROVED: continue

# 4. Implement
"Use the developer agent to implement the approved design"
# → Developer reads design + review notes, implements code

# 5. Test
"Use the tester agent to write tests for the implementation"
# → Tester writes tests, writes to .claude/handoffs/test-results.md

# 6. Test Review
"Use the reviewer agent to review test coverage"
# → Reviewer reads test results, writes to .claude/handoffs/review-tests.md
# → If NEEDS CHANGES: tester adds tests, re-review
# → If APPROVED: feature complete

# 7. Deploy (if needed)
"Use the devops agent to deploy to staging"
# → DevOps reads project skills for deployment commands
```

### Agent Invocation Patterns

```bash
# Explicit invocation
"Use the architect agent to..."

# Let Claude auto-delegate (based on agent descriptions)
"Design the authentication system"  # → triggers architect

# Quick fixes bypass the workflow
"Use simple-dev to fix the typo in error message"

# Check handoff files
"What's in .claude/handoffs/design.md?"

# Resume after review
"The design was approved, use developer to implement"
```

### Agile Workflow: Small Iterations

Work in small, integrable chunks through the stages:

**Instead of:** Design entire auth system → implement everything → test everything
**Do this:** Design login endpoint → implement → test → integrate → move to next endpoint

**Key practices:**
- Architect: Break designs into independently implementable pieces
- Developer: Implement one piece at a time, integrate as you go
- Tester: Test each piece as it's completed
- Move through stages quickly with small changes rather than big batches

**Benefits:**
- Earlier feedback from reviews
- Easier to course-correct
- Continuous integration reduces merge complexity
- Faster time to first working increment

## Customization

### Setting Up Knowledge Base for Your Project

The knowledge-base agent generates architectural documentation from your existing code.

```bash
# Copy KB skill for your language to project
cp -r project-template/.claude/skills/python_kb .claude/skills/    # for Python
# OR
cp -r project-template/.claude/skills/kotlin_kb .claude/skills/   # for Kotlin/Android

# Use the knowledge-base agent
"Use knowledge-base agent to document the codebase"
```

The agent will:
- Use language-specific tools to generate diagrams from code
- Create module/package dependency graphs
- Generate class diagrams for each module
- **Embed all diagrams in markdown files with descriptions** (not separate .mermaid files)
- Write architecture summaries to `overview/architecture-summary.md`
- Write module docs to `modules/<module-name>.md`

**Note:** Knowledge-base documents EXISTING code. For new features, use architect agent (designs go to handoffs, then archive to knowledge-base after completion).

**Diagram Philosophy:** All Mermaid diagrams are embedded directly in markdown files with descriptions before and after, making them analyzable directly in the documentation.

### Adding Project-Specific DevOps Skills

Create `.claude/skills/deployment/SKILL.md`:
```markdown
---
name: deployment
description: Project-specific deployment instructions
---

## Deployment Commands

### Staging
\`\`\`bash
./deploy.sh staging
\`\`\`

### Production
\`\`\`bash
./deploy.sh prod --require-approval
\`\`\`

## Environments
- Staging: https://staging.example.com
- Production: https://example.com
```

### Adding a New Agent

Create in `~/.claude/agents/` (global) or `.claude/agents/` (project):
```markdown
---
name: security-auditor
description: Security specialist. Use before deployment to audit for vulnerabilities.
tools: Read, Glob, Grep
model: sonnet
---

You are a Security Auditor. You review code for security vulnerabilities.

## First Steps
1. Read `knowledge-base/INDEX.md`
2. Focus on: auth, input validation, secrets, SQL injection, XSS

## Your Outputs
Report findings with severity levels.
```

## Tips

1. **Keep knowledge-base updated** - Agents rely on it for context
2. **Use knowledge-base agent when starting** - Document existing code structure before adding features
3. **Clear handoffs after feature completion** - Archive approved designs to knowledge-base
4. **Review feedback is valuable** - Even "APPROVED" reviews document what was checked
5. **Mermaid diagrams are searchable** - Architects should use them liberally
6. **Skills for variations** - Use skills for project-specific commands, not agent rewrites

## Troubleshooting

### Agent not using knowledge-base?
Make sure `knowledge-base/INDEX.md` exists and is populated.

### Handoffs getting stale?
Clear or archive `.claude/handoffs/` between features.

### DevOps agent too generic?
Add project-specific skills in `.claude/skills/`.

## Resources

- [Claude Code Subagents Docs](https://docs.anthropic.com/claude-code/subagents)
- [VoltAgent's 100+ agents](https://github.com/VoltAgent/awesome-claude-code-subagents) (for inspiration)
