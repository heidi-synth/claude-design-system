# Claude Design System — Setup and configuration guide

**Version:** 1.1
**Last updated:** 2026-03-08
**Maintainer:** Heidi Hayashitani

Single source of truth for installing, configuring, and getting value from Design System Ops. Update it when the product changes.

---

## Prerequisites

Before installing, you need:

1. **Claude Code** — the Anthropic CLI that runs Claude from your terminal. Get it at [claude.ai](https://claude.ai). (Cowork users: skip this — just drag and drop the `.plugin` file.)
2. **Node.js 18+** — only required if you use Style Dictionary to build tokens. Check with `node --version`. If you see `v18.0.0` or higher, you're good.
3. **Your design system source files** — at minimum, your token files (JSON, SCSS, CSS, whatever format) and component source code (React, Vue, Twig, etc.)
4. **Figma desktop app** (optional) — some skills can read your Figma components and variables directly. Every skill works without it.

---

## Step 1: Install the skill pack

Clone the repo into your Claude Code skills directory. The `~` symbol is your home folder (`/Users/yourname/` on Mac, `/home/yourname/` on Linux):

```bash
git clone https://github.com/heidi-synth/claude-design-system.git ~/.claude/skills/design-system-ops
```

This creates `design-system-ops` inside the hidden `.claude/skills/` directory in your home folder. The `.claude` folder is hidden by default — on Mac, press `Cmd+Shift+.` to show hidden files.

For project-scoped install (not global):

```bash
git clone https://github.com/heidi-synth/claude-design-system.git your-project/.claude/skills/design-system-ops
```

**Example:** If your project lives at `~/projects/my-design-system`:

```bash
git clone https://github.com/heidi-synth/claude-design-system.git ~/projects/my-design-system/.claude/skills/design-system-ops
```

After cloning, the structure should look like:

```
design-system-ops/
├── skills/              39 skills organised by category
├── commands/            13 command definitions
├── knowledge-notes/     11 reference documents (canonical)
├── sample-outputs/      example outputs for reference
├── .claude-plugin/      plugin manifest for Cowork
└── .ds-ops-config.yml   annotated configuration template
```

**Verify:** Run any skill by name in Claude Code — "Run token-audit on my token files." Claude should find and load it. If it doesn't, check the skills directory path in your Claude Code config.

---

## Step 2: Prepare your design system inputs

Each skill needs specific source material. Here's what to prepare for the most common starting workflows:

### For token auditing and compliance

Gather your token files in whatever format your system uses:

| Format | What to provide | Example path |
|---|---|---|
| JSON / DTCG | Token JSON files | `src/tokens/colors.json` |
| Style Dictionary | Config + source files | `style-dictionary/config.json` |
| CSS custom properties | CSS files with `:root` vars | `src/styles/tokens.css` |
| SCSS variables | SCSS partials with `$` vars | `src/styles/_variables.scss` |
| TypeScript objects | Exported token objects | `src/tokens.ts` |
| Tailwind config | `tailwind.config.js` or `.ts` | `tailwind.config.ts` |

No format conversion needed — the skills accept any of these.

### For component auditing

Provide:
- Your component source directory (e.g. `src/components/`)
- Your Storybook instance URL (if published)
- Your npm package name (if published to a registry)

### For documentation skills

Provide:
- Component source files — the skills read props, variants, and implementation details directly
- Any existing documentation to incorporate

### For governance skills

Provide:
- Your team's contribution workflow (even if it's informal)
- Any existing ADRs or decision records
- Access to your repo's PR/issue history (for contribution tracking)

---

## Step 3: Run your first skill

Pick the entry point that matches your immediate need:

### Option A: "I want to understand the state of my system"

Run `system-health` — the broadest assessment, covering seven dimensions.

```
Prompt: "Run system-health on my design system.
Here are my token files: [path to tokens]
Here is my component library: [path to components]
We have approximately [N] components used by [M] teams."
```

### Option B: "I want to audit my tokens"

Run `token-audit` for a detailed tier analysis with specific findings.

```
Prompt: "Run token-audit on [path to token files].
The tokens are in [format: JSON/SCSS/CSS vars/TypeScript/Tailwind config]."
```

### Option C: "I want to write AI-optimised component descriptions"

Run `ai-component-description` — the differentiating skill. Produces six-section descriptions structured for Figma MCP and LLM consumption.

```
Prompt: "Run ai-component-description on [path to Button component].
The component uses [framework: React/Vue/Twig] with [styling: Tailwind/SCSS/Emotion]."
```

### Option D: "I want the full picture"

Run the `full-system-diagnostic` agent — chains five skills and produces a unified report with cross-skill pattern analysis. Systems under 5 components will be redirected to individual skills instead.

```
Prompt: "Run full-system-diagnostic on my design system.
Token files: [path]
Component library: [path]
Documentation: [URL or path]
Approximate size: [N] components, [M] consuming teams."
```

---

## Step 4: Configure your team (optional)

Every skill works on sensible defaults — skip this if you want to get moving fast.

To customize skill behavior, create `.ds-ops-config.yml` in your project root (same folder as `package.json`). It controls three things:

### Severity calibration

Tells skills how strictly to flag different problem types. By default, a hardcoded color is flagged as "High." If your team actively supports theming, that same hardcoded color is actually critical — you can tell the skills to treat it that way.

```yaml
severity:
  hardcoded_color: critical    # We actively theme — hardcoded colours break theming
  wrong_tier_reference: critical
  naming_violation: high       # Our naming conventions are formalised and enforced
```

Every skill that produces findings (`token-audit`, `token-compliance`, `drift-detection`, etc.) reads these settings before classifying. Without this file, skills use defaults that work for most teams.

### Recurring workflow

Turns one-off audits into a monitoring system. Without it, each run is a fresh start. With it, each run compares against the last — "17 new violations since Q3, 9 resolved, 23 persistent."

```yaml
recurring:
  output_directory: ".ds-ops-reports/"   # Where reports are saved
  naming_pattern: "{skill}-{date}"       # e.g. "token-audit-2026-03-09.md"
  comparison_mode: "full"                # Compare every finding, not just summaries
  retain_count: 8                        # Keep the last 8 reports, delete older ones
```

When configured, each skill run:
1. Checks `.ds-ops-reports/` for a previous report
2. Compares current findings against last run
3. Adds a "What changed" section — new, resolved, and persistent findings
4. Saves the new report and prunes beyond the last 8

**Skills that support recurring:** `token-audit`, `drift-detection`, `system-health`, `adoption-report`, and all four agents.

### Release gates

Override the `component-to-release` agent's release gates for agreed team exceptions:

```yaml
gates:
  accessibility:
    contrast_blocks_release: false  # Brand transition in progress — tracking timeline separately
```

A full annotated config template ships with the product at `.ds-ops-config.yml`.

---

## Step 5: Configure integrations (optional)

Every skill works without integrations — you just provide files and data manually in the conversation. Integrations let skills pull data automatically so you don't have to.

### Figma MCP server (HIGH value)

Lets Claude read your Figma file directly — components, variables, styles, analytics. Skills pull data straight from Figma instead of relying on descriptions or screenshots.

**Setup:**

1. Install the Figma MCP server — follow [Figma's official guide](https://help.figma.com/hc/en-us/articles/32132100833559-Guide-to-the-Figma-MCP-server)
2. Add the Figma MCP server in your Claude Code settings (the guide covers this)
3. Generate a Figma personal access token: Figma → Settings → Personal access tokens → Generate
4. Add your Figma file key to `.ds-ops-config.yml`:
   ```yaml
   integrations:
     figma:
       enabled: true
       file_key: "abc123def456"  # The string after /design/ in your Figma URL
   ```
   **Finding your file key:** Open your Figma file. The URL looks like `figma.com/design/abc123def456/My-File` — `abc123def456` is your key.
5. Test it: "List the variable collections in my Figma file." Real data = working connection.

**Skills that use this:** `ai-component-description`, `component-audit`, `design-to-code-check`, `drift-detection`, `token-audit`, `token-documentation`, `system-health`, `adoption-report`

### Style Dictionary v4 (HIGH value)

Enables automatic token parsing, DTCG compliance checking, and multi-platform token transformation. Token skills parse automatically instead of requiring manual file input.

**Setup:**
1. Install Style Dictionary: `npm install -g style-dictionary@4`
2. Add to `.ds-ops-config.yml`:
   ```yaml
   integrations:
     style_dictionary:
       enabled: true
       config_path: "style-dictionary/config.json"
   ```

**Skills that auto-pull:** `token-audit`, `token-compliance`, `token-documentation`

### GitHub API (MEDIUM-HIGH value)

Lets Claude search your repo automatically — scanning for hardcoded values, reading component source, checking PR activity — instead of requiring you to paste file contents.

**Setup:**

1. Create a GitHub Personal Access Token:
   - GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
   - Generate new token (classic), select scopes: `repo` and `read:org`
   - Copy the token (starts with `ghp_`)
2. Add to your shell profile (`~/.zshrc` or `~/.bashrc`):
   ```bash
   export GITHUB_TOKEN=ghp_your_token_here
   ```
   Restart your terminal or run `source ~/.zshrc`.
3. Add to `.ds-ops-config.yml`:
   ```yaml
   integrations:
     github:
       enabled: true
       repo: "yourorg/design-system"
   ```

**Skills that use this:** `token-compliance`, `token-audit`, `drift-detection`, `component-audit`, `adoption-report`, `system-health`, `ai-component-description`

### npm registry API (MEDIUM value)

Pulls download stats and version distribution for adoption tracking automatically.

**Setup:** No auth required for public packages. For private packages, configure `.npmrc` with the appropriate registry token.
```yaml
integrations:
  npm:
    enabled: true
    package_name: "@yourorg/design-system"
    scoped_packages: ["@yourorg/button", "@yourorg/card"]  # For monorepos
```

**Skills that auto-pull:** `adoption-report`, `component-audit`, `system-health`

### Chromatic / Storybook (MEDIUM value)

Pulls visual regression data, component story inventory, and documentation coverage.

```yaml
integrations:
  storybook:
    enabled: true
    url: "https://storybook.yourorg.com"
  chromatic:
    enabled: true
    app_id: "your-app-id"
```

**Skills that auto-pull:** `component-audit`, `drift-detection`, `design-to-code-check`, `ai-component-description`

### Documentation platform (MEDIUM value)

Cross-references your docs platform to identify under-documented components.

**Supported:** Zeroheight, Supernova, Storybook (docs tab)

```yaml
integrations:
  documentation:
    enabled: true
    platform: "zeroheight"       # or "supernova" or "storybook"
    url: "https://docs.yourorg.com"
    api_key_env: "ZEROHEIGHT_API_KEY"
```

**Skills that auto-pull:** `component-audit`, `system-health`, `adoption-report`, `token-documentation`

---

## Step 6: The knowledge note system

Skills are powered by bundled knowledge notes — expert reference material that makes output production-grade instead of generic. Each skill file has a `references:` section in its YAML frontmatter pointing to the knowledge notes it needs. Claude reads those notes before running the skill.

When you edit a knowledge note in `knowledge-notes/`, every skill that references it picks up the change immediately — no syncing needed.

| Note | What it provides |
|---|---|
| `token-architecture` | Three-tier model, naming conventions, reference rules, common failures |
| `component-governance` | Contribution criteria, deprecation triggers, lifecycle patterns |
| `ai-readiness` | Six dimensions of component AI readiness, description format |
| `design-to-code-contract` | Design, build, documentation, and release contract definitions |
| `component-bestiary-reference` | Challenge Rating system for documentation depth calibration |
| `agent-orchestration-guide` | Multi-agent coordination patterns and context management |
| `human-oversight-framework` | Human-in-the-loop validation for AI agent workflows |
| `mcp-setup-guide` | Three-layer MCP architecture for design system tooling |
| `context-engine-blueprints` | YAML output templates for all seven context engine blueprints |
| `output-discipline` | Shared quality standards across all skill output |

---

## Step 7: Use the sample outputs as benchmarks

The `sample-outputs/` directory has three real skill outputs to compare against:

| File | What it demonstrates |
|---|---|
| `example-token-audit.md` | Full token audit against a ~480 token system. Shows finding format, severity levels, DTCG compatibility assessment, and remediation priority. |
| `example-component-description.md` | Complete six-section Figma MCP description for a React Dialog component. |
| `example-health-dashboard.html` | Interactive HTML dashboard from audit findings — open in a browser. |

If your first skill run is significantly less detailed than the sample, the knowledge notes likely didn't load. Check that the `references:` paths in the skill file resolve correctly to `knowledge-notes/`.

---

## Framework compatibility

### React (JSX/TSX)

Default assumption. All skills work natively. Styling supported: CSS Modules, SCSS, Tailwind, Emotion, styled-components, CSS custom properties.

### Vue SFC (.vue files)

Skills recognize single-file component structure (`<template>`, `<script>`, `<style>`). Note your styling approach in the prompt:
- Scoped SCSS: `<style lang="scss" scoped>` — violations appear as raw values in style blocks
- CSS custom properties: `var(--token)` references in scoped styles
- `v-bind()` for dynamic CSS: valid token usage when bound to token-backed props

### Twig / Fractal

Skills recognize Atomic Design directory conventions (`01-atoms/`, `02-molecules/`, `03-organisms/`). Components identified by `.twig` + `.config.yml` pairs. Token references are typically via BEM utility classes — audit the backing SCSS, not the template. Inline `style=""` attributes in templates are always violations.

### Emotion / CSS-in-JS with TypeScript

Skills recognize TypeScript token objects (`export const tokens = { ... }`), theme objects, and helper functions. Token violations are string literals in style objects: `color: '#FF0000'`, `padding: '16px'`.

### Tailwind CSS

Skills distinguish token references (utility classes mapped to your Tailwind config) from hardcoded values (arbitrary value brackets: `h-[12px]`, `bg-[#ff0000]`). Standard utility classes that resolve to configured tokens are not violations.

---

## Monorepo considerations

- **Per-package npm downloads are unreliable** — use import analysis across consuming products instead
- **Versioning patterns:** `-next` or `-v2` suffixed packages indicate in-flight migrations. Count both, flag the pair.
- **Private vs. public components:** Underscore-prefixed components, anything in `internal/`, and components not re-exported from barrel files are internal — exclude from public counts.
- **Utility vs. user-facing:** Layout primitives (`Box`, `Stack`, `Flex`, `VisuallyHidden`) are infrastructure. Categorize separately.

---

## Recommended workflow sequences

### Quarterly review

1. Run `full-system-diagnostic` (or individual skills if < 15 components)
2. Run `stakeholder-brief` using the diagnostic as input
3. Run `adoption-report` if usage data is available
4. Share with leadership

### Pre-release validation (per component)

1. `design-to-code-check` against the Figma spec
2. `accessibility-per-component`
3. `token-compliance` on the component's source
4. `ai-component-description` to generate the Figma description
5. `usage-guidelines` for the documentation page
6. `change-communication` to draft the release announcement

Or use `component-to-release` — it chains these automatically.

### New system lead onboarding

1. `system-health` for the big picture
2. `component-audit` for the component inventory
3. `token-audit` for the token architecture assessment
4. Read the knowledge notes directly for the frameworks behind the skills

---

## Troubleshooting

**"The skill didn't load the knowledge notes"**

1. Open the skill file (e.g. `skills/audit/token-audit.md`)
2. Find the `references:` section in the YAML frontmatter at the top
3. Verify each path resolves to a file in `knowledge-notes/` (e.g. `../../knowledge-notes/token-architecture.md`)
4. If any files are missing, check your install — the repo should include all of them

**"The output is generic / not specific enough"**

The skill didn't have access to your actual files. Point Claude at the source directly instead of describing it.

Instead of: *"we have about 200 tokens in three tiers"*
Use: *"Audit the tokens in `src/tokens/`"*

**"The skill doesn't understand my token format"**

Tell it explicitly.

- "My tokens are in CSS custom properties in `src/styles/tokens.css`"
- "We use SCSS variables — `$color-primary: #0f62fe`"
- "Our tokens are in DTCG JSON format, managed by Style Dictionary"
- "Tokens are TypeScript objects exported from `src/tokens.ts`"

If the format still isn't recognized, the skill will infer structure from naming patterns — it usually gets there.

**"The monorepo structure is confusing the audit"**

Tell Claude it's a monorepo in your prompt:

*"Audit our component library. This is a monorepo — packages are in `packages/`. The main library is `packages/core/`."*

The skill handles versioning patterns, private vs. public components, utility classification, and unreliable per-package download counts automatically from there.
