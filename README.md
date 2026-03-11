# Claude Design System


Claude Code skills for the work that keeps a design system alive (and in use!!).

---

## Design tooling built by a designer, for designers

# The Drift

Tokens go stale.
Components fork in the dark.
The governance doc yellows at the edges.
The deprecation plan lived in Marcus's head,
But Marcus left in 2022.
There are tools for the designers,
the ones who consume, drag, drop, and ship.
But the keepers of the system?
They had spreadsheets, and Storybooks, Notion pages no one read.
But now, they have code agents who can help them do the work.
So, roll up the sleeves on your shirt.
Open that iTerm, and smash that bash.
Things are moving fast and it's a mad dash.

---

## What this is?

A pack of skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) and [Cowork](https://claude.ai) that teaches Claude how to do design systems work the way a staff-level designer would. A harness with structured processes, expert frameworks, and output calibrated to the work enterprise designers do every day.

This isn't a smarter prompt. 
It's a different kind of output.

Ask Claude to audit your tokens (it reads your actual files). Flags naming violations. Identifies tier inconsistencies or drift. 
If you're migrating to DTCG, it hands you a sprint-ready plan with hour estimates, not a doc you have to interpret.

This is what proper DS tooling looks like.

This collection of skills is a combination of skills I have written myself, as well other helpful skills I have found around the internet. 
All skills have been tested for maliciousness (to the best of my ability I am not a securities engineer).

**Who it's for:** Anyone who is responsible for a production design system. The DS leads, the UI Frameworks engineers. The people who build and maintain the machine. 
This is not for UI/UX designers who are consumers of the DS.

---

## Install

### Cowork (desktop app — easiest)

1. Download `design-system-ops.plugin` from the [`installable/`](installable/) folder
2. Open the Claude desktop app and start a Cowork session
3. Drop the `.plugin` file into the chat
4. Follow the install prompt — done

### Claude Code (terminal)

Clone this repo into your Claude Code skills directory:

```bash
git clone https://github.com/heidi-synth/claude-design-system.git ~/.claude/skills/design-system-ops
```

For project-level installation (instead of global):

```bash
git clone https://github.com/heidi-synth/claude-design-system.git your-project/.claude/skills/design-system-ops
```

**Verify:** Open Claude Code and say "Is my design system healthy?" If Claude responds with a structured, multi-step process to nurse it back to health, you're good to go.

See [1-INSTALL.md](1-INSTALL.md) for the full guide with entry points by use case.

---

## What's inside

### Skills

| Category | Skills | What they do |
|----------|--------|-------------|
| **Audit** | token-audit, component-audit, system-health, drift-detection, naming-audit, figma-variable-audit, codebase-index, system-benchmark, theme-audit | Understand what you actually have |
| **Govern** | contribution-workflow, deprecation-process, decision-record, change-communication, backlog-generator, version-bump-advisor, release-retrospective, governance-encoder, session-memory, codemod-generator, triage | Run the system as infrastructure |
| **Document** | ai-component-description, pattern-documentation, token-documentation, usage-guidelines, component-decision-tree, context-engine-builder, metadata-schema-generator | Make the system legible to humans and machines |
| **Validate** | design-to-code-check, accessibility-per-component, token-compliance, schema-validator, component-api-validator, cicd-integration | Verify quality before it ships |
| **Communicate** | adoption-report, stakeholder-brief, system-pitch, designer-onboarding, engineering-onboarding, visual-report | Move people and decisions |

### Agents

| Agent | What it chains | When to use it |
|-------|---------------|----------------|
| `/full-diagnostic` | 5 audit skills with cross-skill synthesis | Quarterly review or inheriting a system |
| `/release-check` | Design-to-code, accessibility, token compliance, AI description, usage guidelines, change communication | Before shipping any component |
| `/governance-review` | Adoption report, drift detection, stakeholder brief | Monthly or quarterly governance cadence |
| `/migration` | Token audit, naming audit, migration plan, codemod generation, communication | Planning a major migration |

### AI infrastructure skills

Five skills produce machine-readable files that AI agents and tooling consume directly: `context-engine-builder`, `governance-encoder`, `codebase-index`, `component-decision-tree`, and `metadata-schema-generator`. If you're building AI-native design system infrastructure, these are the files your agents need.

---

## Core frameworks

The skills encode specific design system expert frameworks:

- **Three-tier token architecture** — primitive → semantic → component, with tier-leakage detection and DTCG 2025.10 alignment
- **Component Challenge Rating** — a difficulty classification system that calibrates audit depth and remediation estimates to actual component complexity
- **Design system maturity model** — five levels from ad-hoc to optimised, used across health reports, stakeholder briefs, and adoption tracking to frame recommendations appropriately
- **AI-readiness scoring** — evaluates how well your system's metadata, naming, and structure support AI agent consumption

---

## Quick examples

Tip: You don't need to memorize skill names. Describe what you need and the right skill will activate!

**"I need to sunset or deprecate a component or component set"**
→ Say: "Deprecate ToggleBig and ToggleDefault in favor of Toggle"

**"I don't know the objective state of my ds"**
→ Say: "How healthy is my design system?"

**"Our tokens are a disaster"**
→ Say: "Audit my design system tokens"

**"How do I communicate the value design systems offer?"**
→ Say: "Write a stakeholder brief for leadership about our design system and it's objective value"


---

## Sample outputs

Real outputs from real codebases are in [`sample-outputs/`](sample-outputs/):

- **[example-token-audit.md](sample-outputs/example-token-audit.md)** — A complete audit of a ~480 token system: 11 findings across Critical/High/Medium/Low, specific code examples, DTCG alignment assessment, and a prioritised remediation roadmap.
- **[example-component-description.md](sample-outputs/example-component-description.md)** — A six-section MCP description for a React Dialog component: ~620 words of structured plain text for Figma.
- **[example-health-dashboard.html](sample-outputs/example-health-dashboard.html)** — An interactive HTML dashboard generated from audit findings: health radar, severity distribution, priority matrix, metric cards. Open in any browser.

---

## Configuration

Every skill works out of the box with no configuration. If you want to customise behaviour — severity overrides, Figma integration, GitHub API access, recurring trend tracking — create a `.ds-ops-config.yml` in your project root. An annotated template is included in the skill pack.

See [3-SETUP-AND-CONFIG.md](3-SETUP-AND-CONFIG.md) for the full configuration reference.

---

## Figma integration (optional)

Several skills become more powerful when Claude can read your Figma file directly. Two options:

- **[Figma Console MCP](https://github.com/southleft/figma-console-mcp)** (recommended) — Read and write access. Skills can write descriptions back into components, rename variables, create tokens.
- **[Standard Figma MCP](https://help.figma.com/hc/en-us/articles/32132100833559-Guide-to-the-Figma-MCP-server)** — Read-only access. Skills pull component data and variable collections.

Setup details in [1-INSTALL.md](1-INSTALL.md#setting-up-figma-integration).

---

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) or [Cowork](https://claude.ai) (Claude desktop app)
- Access to your design system's source files (tokens, components, config)
- Works with any stack: React, Vue, Twig, Tailwind, SCSS, CSS custom properties, Style Dictionary, Emotion, CSS-in-JS

---

## Documentation

| File | What it covers |
|------|---------------|
| [1-INSTALL.md](1-INSTALL.md) | Full installation guide with entry points by use case |
| [2-WHATS-INCLUDED.md](2-WHATS-INCLUDED.md) | Complete product documentation — every skill, agent, and knowledge note |
| [3-SETUP-AND-CONFIG.md](3-SETUP-AND-CONFIG.md) | Deep-dive setup, framework compatibility, monorepo handling, troubleshooting |

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

---

## License

MIT — see [LICENSE](LICENSE).

---


## Author

**Heidi Hayashitani** — (mailto:heidihayashitani@gmail.com)
Additional contributions from people around the internet. 
I was messy in how I collected things and cannot attribute by name, however; I thank you!

