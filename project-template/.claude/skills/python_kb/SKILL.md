---
name: python_kb
description: Python knowledge base generation - tools and commands for creating architectural diagrams
---

# Python Knowledge Base Tools

## Required Tools

```bash
pip install pydeps pylint
```

## Package Dependencies

**Tool:** pydeps - analyzes import statements

```bash
# DOT format (text-based, can convert to Mermaid with AI)
pydeps <package_name> --only <package_name> -T dot -o dependencies.dot

# With clustering and limited depth
pydeps <package_name> --only <package_name> --cluster --max-bacon 2 -T dot -o dependencies.dot

# Exclude specific modules
pydeps <package_name> --only <package_name> --exclude tests,migrations -T dot -o dependencies.dot
```

**Key options:**
- `--only <package>` - exclude stdlib/third-party
- `--cluster` - group by subpackage
- `--max-bacon N` - limit depth
- `--exclude` - exclude modules
- `-T dot` - output DOT format

**Convert DOT to Mermaid:** Use AI to convert DOT format to Mermaid graph

## Class Diagrams

**Tool:** pyreverse (part of pylint) - generates UML from source

```bash
# Mermaid format (direct output)
pyreverse -o mmd -p <project_name> <package_path>/

# Public members only
pyreverse -o mmd --filter-mode=PUB_ONLY -p <project_name> <package_path>/

# Specific classes
pyreverse -o mmd --class=MyClass,OtherClass -p <project_name> <package_path>/

# Ignore patterns
pyreverse -o mmd --ignore=tests -p <project_name> <package_path>/
```

Output: `classes_<project_name>.mmd` (Mermaid format)

**Alternative formats:** `-o dot` (Graphviz DOT), `-o puml` (PlantUML)

## Typical Workflow

```bash
# 1. Package dependencies (DOT format - will be converted to Mermaid and embedded)
pydeps myapp --only myapp --cluster -T dot -o package-deps.dot

# 2. Class diagrams per module (Mermaid format - will be embedded in module markdown)
pyreverse -o mmd -p auth myapp/auth/
# Output: classes_auth.mmd

pyreverse -o mmd -p core myapp/core/
# Output: classes_core.mmd

# 3. Knowledge-base agent will:
#    - Convert DOT to Mermaid
#    - Embed diagrams in markdown files with descriptions
#    - Create overview/architecture-summary.md with dependency graph
#    - Create modules/<module-name>.md with class diagrams
```

## Project Customization

Update this file with:
- Your package name
- Modules to document
- Exclusion patterns
- CI integration commands
