# DNA Seq Genomics Marketplace

Give Claude and Codex focused, research-use genomics capabilities from one
catalog. Start with a gene or variant in Ensembl, investigate published
polygenic-score models, or score a local genome while keeping the evidence and
limitations visible.

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
- **`just-dna-agents`** researches, validates, and compiles genetics annotation
  modules. It remains Claude-only while its broader dependency model is
  designed separately.

`ensembl` and `just-prs` are available in both catalogs. They complement one
another but are not coupled: Ensembl explains individual loci and biological
consequences, while just-prs evaluates aggregate predisposition across many
variants.

## Configure Claude Code

Register the marketplace once, then install either or both plugins:

```bash
claude plugin marketplace add dna-seq/dna-seq-claude-marketplace
claude plugin install ensembl@dna-seq
claude plugin install just-prs@dna-seq
```

The existing Claude-only module-authoring plugin is optional:

```bash
claude plugin install just-dna-agents@dna-seq
```

Inspect installed plugins with `claude plugin list`. Use
`claude plugin update ensembl@dna-seq` or
`claude plugin update just-prs@dna-seq` to receive a newer release.

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
