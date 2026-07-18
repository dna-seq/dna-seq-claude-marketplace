# DNA Seq Claude Code Marketplace

Install research-use genomics capabilities for Claude Code from one catalog.
Each plugin remains independently versioned and ships its own MCP server,
skills, and safety guidance.

## Install

```bash
claude plugin marketplace add dna-seq/dna-seq-claude-marketplace
claude plugin install just-prs@dna-seq
claude plugin install just-dna-agents@dna-seq
```

Use `claude plugin update` to receive newer plugin releases, or inspect
installed plugins with `claude plugin list`.

## Available plugins

| Plugin | Purpose | Runtime |
| --- | --- | --- |
| `just-prs` | PGS Catalog search, local polygenic-score computation, quality assessment, and evidence-aware trait interpretation. | `just-prs-mcp` from PyPI via `uvx` |
| `just-dna-agents` | Genetics annotation-module research, validation, and compilation. | `just-dna-agents-mcp` from PyPI via `uvx` |

The plugins are complementary but independent. `just-prs` analyzes a local
genome with published PGS models. `just-dna-agents` authors deployable
annotation modules from evidence. Both are research-use tools, not medical
advice.

## Development and validation

This repository is a Claude Code marketplace catalog, not a Python package, so
it deliberately has no empty `pyproject.toml` or uv environment. Each listed
plugin owns its Python/uv runtime and dependency lockfiles.

Validate the catalog with Claude Code:

```bash
claude plugin validate .
```

Test installation from a local checkout:

```bash
claude plugin marketplace add . --scope local
claude plugin install just-prs@dna-seq --scope local
```

## Add a plugin

1. Ensure the source repository contains a valid
   `.claude-plugin/plugin.json`.
2. Ensure its MCP configuration launches a published, versioned package (for
   Python tools, normally through `uvx`).
3. Add an entry to `.claude-plugin/marketplace.json` using a GitHub source.
4. Run `claude plugin validate .`.

Plugin sources may live in a different repository than this catalog. Keep
plugin manifests and versions in the source repository; this marketplace is
only the discovery and installation index.
