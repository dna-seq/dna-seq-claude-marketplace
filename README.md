# DNA Seq Genomics Marketplace

Give Claude and Codex focused, research-use genomics capabilities from one
catalog. Start with a gene or variant in Ensembl, investigate published
polygenic-score models, score a local genome while keeping the evidence and
limitations visible, or author and publish a just-dna annotation module of your
own.

Each plugin is independently versioned. Its source repository owns the MCP
runtime, skills, tests, and safety guidance; this repository provides discovery
for both Claude and Codex.

## Choose a plugin

- **`ensembl`** looks up genes, transcripts, proteins, variants, population
  frequencies, phenotypes, genomic regions, and Refget sequences. It sends
  lookup identifiers and coordinates to public Ensembl services.
- **`just-prs`** searches the PGS Catalog, compares models, scores a local VCF,
  assesses coverage, and interprets model agreement. It runs locally by
  default; personal genome files are not uploaded.
- **`just-module-creator`** authors just-dna annotation modules: scaffold the
  tables, draft rows from ClinVar, find and check the literature behind each
  row, lint, validate, compile to parquet, and publish to the module registry.
  Its schema answers are generated from the live `just-dna-format` models, so
  they cannot drift from what the compiler accepts. Requires `uv` and Python
  3.13 or newer; the server boots with nothing configured, and only publishing
  needs a registry account.
- **`just-dna-agents`** wraps the same annotation work in subagents and slash
  commands. It is not retired and is not a competitor: the likely direction is
  that it grows into the wider toolkit and re-exposes `just-module-creator`
  underneath. Until that lands, reach for `just-module-creator` to author a
  module, and for `just-dna-agents` when you want its agents and commands.

`ensembl` and `just-prs` are available in both catalogs. They complement one
another but are not coupled: Ensembl explains individual loci and biological
consequences, while just-prs evaluates aggregate predisposition across many
variants. `just-module-creator` and `just-dna-agents` remain Claude-only —
neither ships a `.codex-plugin` manifest yet.

## Configure Claude Code

Register the marketplace once, then install whichever plugins you need:

```bash
claude plugin marketplace add dna-seq/dna-seq-claude-marketplace
claude plugin install ensembl@dna-seq
claude plugin install just-prs@dna-seq
```

The Claude-only module-authoring plugins are optional:

```bash
claude plugin install just-module-creator@dna-seq
claude plugin install just-dna-agents@dna-seq
```

`just-module-creator` launches its MCP server with
`uv run --project ${CLAUDE_PLUGIN_ROOT}`, so `uv` must be on PATH;
dependencies install on first use.

Inspect installed plugins with `claude plugin list`.

### Updating

Each plugin is versioned in its own repository, so installing once does not
track new releases automatically — refresh the catalog, then update the
plugin itself:

```bash
claude plugin marketplace update dna-seq   # refresh the catalog (this repo)
claude plugin update ensembl@dna-seq       # pull the plugin's latest release
claude plugin update just-prs@dna-seq
claude plugin update just-module-creator@dna-seq
```

`claude plugin marketplace update` refreshes `marketplace.json` (e.g. a new
plugin was added, or one was renamed); `claude plugin update <plugin>` pulls
that plugin's own source to its latest commit/release. Restart Claude Code
(or start a new session) afterward so the updated version loads — run
`claude plugin list` to confirm what is currently installed.

## Configure Codex Desktop

Codex Desktop discovers repository marketplaces from
`.agents/plugins/marketplace.json`.

1. Clone this repository and open the checkout as a project in Codex Desktop
   (`/usr/bin/codex-desktop` on Linux installations that use the desktop
   package).
2. Open **Plugins**, select **DNA Seq Genomics**, and install `ensembl`,
   `just-prs`, or both.
3. Start a new Codex session after enabling a plugin so its skills and MCP
   servers load.

If a Codex CLI is already installed, the equivalent marketplace registration
is:

```bash
codex plugin marketplace add dna-seq/dna-seq-claude-marketplace
```

The CLI is optional; do not install a second Codex distribution just for this
marketplace.

### Updating

Codex Desktop reads the marketplace from your local checkout, so pick it up
with a normal pull, then restart:

```bash
cd dna-seq-claude-marketplace
git pull
```

Restart Codex Desktop (or start a new session) so it re-reads
`.agents/plugins/marketplace.json` and re-resolves each plugin's `ref: main`
source. With the Codex CLI, the equivalent is:

```bash
codex plugin marketplace update dna-seq
codex plugin update just-prs@dna-seq
```

## Try it

With Ensembl:

```text
Tell me about BRCA2 and identify its MANE Select transcript.
What is rs699? Include population frequencies and predicted consequences.
Convert rs28934578 to genomic, coding, and protein HGVS forms.
```

With just-prs:

```text
Search for well-supported type 2 diabetes scores and compare their evidence.
Explain what information is needed before a PRS percentile is meaningful.
Compute PRS for this local VCF and report coverage, ancestry assumptions,
model agreement, and uncertainty.
```

With just-module-creator:

```text
Which table kind does a CYP2C19 metabolizer finding belong in, and what does
that table require?
Scaffold a module for APOE variants and draft its rows from ClinVar.
Find the papers behind this row and check that the PMID names the one I mean.
Validate and compile this module in strict mode, then tell me whether it would
publish.
```

PRS is genetic predisposition, not a diagnosis or a measurement of current
health. Results depend on model quality, variant coverage, and ancestry match.
All plugins are for research and education, not medical advice.

## Development and validation

This is a catalog, not a Python package. Each plugin repository owns its
Python/uv environment and lockfile.

Validate the Claude marketplace and test local plugin source trees:

```bash
claude plugin validate .
claude --plugin-dir ../ensembl-mcp
claude --plugin-dir ../just-prs-mcp
claude --plugin-dir ../just-module-creator
```

For Codex Desktop, open this checkout and confirm both entries appear under
**DNA Seq Genomics**. Because Git-backed entries resolve their published
branches, use each plugin repository's own tests to verify uncommitted source
changes before publishing.

## Add or update a plugin

1. Keep the Claude manifest and MCP config in the plugin repository.
2. For Codex, provide `.codex-plugin/plugin.json` and a referenced MCP config.
3. Launch published Python runtimes through a version-pinned `uvx` command.
4. Update the appropriate catalog:
   `.claude-plugin/marketplace.json`, `.agents/plugins/marketplace.json`, or
   both.
5. Validate both manifests and run the plugin repository's tests.

Plugin versions remain in their source repositories; this marketplace is only
the discovery and installation index.
