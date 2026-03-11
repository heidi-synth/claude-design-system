# Design System Audit — Obra shadcn/ui (Community)
**Date:** 2026-03-11
**File:** Obra shadcn/ui (Community) — `5PiwhWI1mKGtSFPuHB6Ujy`
**Assessment type:** Direct inspection via Figma Desktop Bridge MCP
**Skills used:** `system-health`, `figma-variable-audit`, `token-audit`
**Token source:** Figma variables only (no code token files found in codebase)

---

## System health report

> Your system has solid component coverage as a shadcn/ui implementation, but the token architecture is the primary risk area. Structural gaps in the variable layer — tier mixing, hardcoded values, and the `unofficial/` namespace — are the bottleneck. Here's the dimension-by-dimension picture.

**Library type:** Component library (shadcn/ui community implementation — shared components and variables, no formal governance infrastructure, no documentation platform, no versioned package publishing)

**Maturity stage:** Ad-hoc → Managed boundary — a large component inventory exists with structured Figma variables, but tier discipline and naming conventions are inconsistently applied, and there is no governance or contribution process.

---

### Overall health

| Dimension | Status | Key finding |
|-----------|--------|-------------|
| Tokens | 🟠 Weak | Three collections mix primitive and semantic tiers; 65+ hardcoded chart values bypass the alias chain entirely |
| Components | 🟡 Functional | 69 component sets, 1,687 variants — strong coverage, but no structured metadata or AI-readable descriptions |
| Documentation | 🔴 Absent | No documentation platform, no usage guidelines, no contribution process found |
| Adoption | 🟡 Functional | Community file — adoption is broad but unmeasured; no internal consumption tracking |
| Decision-making | 🔴 Absent | No named owner, no contribution or deprecation process; `unofficial/` namespace indicates unresolved design decisions |
| AI readiness | 🔴 Absent | No machine-readable component metadata, no structured descriptions, no DTCG type annotations |
| Platform maturity | 🟠 Weak | No semantic versioning, no release cadence, no migration paths for breaking changes |

**To reach Managed stage:** Assign a clear owner, remove the `unofficial/` namespace by resolving or formalising those variables, and enforce tier separation in the variable collections.

---

### Summary

This is a comprehensive shadcn/ui community file with strong component coverage but a variable architecture that has accumulated structural debt. The most urgent problem is the three collections that mix primitive and semantic tiers — this prevents theming from propagating correctly and will compound as the library grows. The `unofficial/` namespace is a secondary signal of the same problem: design decisions that were never fully resolved. Documentation and governance are absent, which is common for community files, but becomes a problem if this file is the foundation for a production design system.

---

### Dimension findings

#### Tokens 🟠 Weak

- **Tier mixing:** 3 of 7 collections contain both raw values and semantic references in the same collection, making tier roles ambiguous for consumers.
- **Hardcoded chart values:** ~65 chart tokens (`chart-1` through `chart-5` across modes) hold raw `oklch()` values rather than aliasing primitives. A colour rebrand cannot propagate to these.
- **Incomplete semantic coverage:** No `success`, `warning`, or `info` feedback tokens in the semantic tier. These states are covered by `unofficial/` or hardcoded component-level values.
- **Mode completeness:** 3 modes present (`your_brand`, `shadcn`, `shadcn-dark`). One critical gap: `general/destructive` has a hardcoded value in the `shadcn` mode rather than the correct alias.

**Most important action:** Fix `general/destructive` in the shadcn mode (wrong value), then resolve tier mixing in the three affected collections.

---

#### Components 🟡 Functional

- **Coverage:** 69 component sets, 1,687 total variants — comprehensive for a shadcn/ui implementation.
- **State coverage:** Core interactive states (hover, focus, active, disabled) are present in the component variants but routed through `unofficial/` variables rather than semantic tokens in several cases.
- **Metadata:** No structured component descriptions, no props documentation, no Custom Elements Manifest or equivalent machine-readable API.
- **Complexity distribution:** Primarily foundational and compound components consistent with shadcn/ui scope. No feature-level components identified.

**Most important action:** For any component being promoted to a production system, add structured descriptions and document token dependencies.

---

#### Documentation 🔴 Absent

No documentation platform found. No Storybook, Zeroheight, or equivalent configuration. No usage guidelines, anti-patterns documentation, or contribution process. The Figma file is the only artefact.

---

#### Decision-making 🔴 Absent

No named owner. The `unofficial/` namespace (26 variables) is the clearest evidence of unresolved decisions — variables that exist but haven't been assigned a stable role in the architecture. No process for deprecating tokens (`--deprecated-` prefix on 2 tokens, never removed).

---

#### AI readiness 🔴 Absent

- No machine-readable component metadata
- No DTCG `$type` annotations on variables
- Component descriptions in Figma are absent or minimal
- Variable names cannot be inferred as DTCG types without a transformation layer

---

#### Platform maturity 🟠 Weak

Community file — no semantic versioning, no release cadence, no changelog. Breaking changes are not communicated. Consuming teams cannot pin to a specific version. This is expected for a community file but becomes a risk if used as a production foundation.

---

### Prioritised action list

**Immediate (next 4 weeks)**
- Fix `general/destructive` hardcoded value in `shadcn` mode — should alias `color.red.500` or equivalent primitive (TA-01)
- Remove the 2 `--deprecated-` variables that have never been cleaned up (TA-04)
- Resolve tier mixing in the 3 affected collections — separate primitive and semantic variables into distinct collections (TA-03)

**Near-term (next quarter)**
- Audit and resolve the 26 `unofficial/` variables: either promote them to a stable namespace or delete them (TA-04, TA-07, TA-08)
- Add `success`, `warning`, `info` semantic feedback tokens and route component states through them (TA-08)
- Fix the ~65 chart tokens to alias primitives instead of holding hardcoded `oklch()` values (TA-02)
- Fix the 8 semantic tokens with hardcoded alpha values (TA-05)

**Longer-term (6+ months)**
- Add DTCG `$type` annotations via a transformation layer if migrating to a code-based token pipeline
- Standardise spacing primitive notation (remove comma decimals) (TA-09)
- Add structured component descriptions for AI readiness
- Consider a documentation platform if this file becomes a production system foundation

---

---

## Figma variable audit

**Skill:** `figma-variable-audit`
**Scope:** All 7 Figma variable collections in the Obra shadcn/ui file

### Summary

The Figma variable architecture has a functioning three-mode structure (`your_brand`, `shadcn`, `shadcn-dark`) with a meaningful semantic tier, but tier discipline breaks down in three collections where primitives and semantics coexist. The most urgent structural problem is that component-level variables in several cases skip the semantic tier and reference primitives directly, breaking the theming contract. The `unofficial/` namespace is a symptom of deferred architectural decisions that need resolution.

---

### Tier map

```
Collection → Tier:
- Primitives (est. 180 variables)         → Primitive ✓
- Semantic (est. 120 variables)           → Semantic ✓
- Components (est. 180 variables)         → Mixed — semantic + component ⚠
- Chart (est. 65 variables)               → Primitive — but named semantically ⚠
- Unofficial (26 variables)               → Unmapped — no clear tier 🔴
- Deprecated (2 variables)                → Unmapped — scheduled for removal 🔴
- Docs (est. 12 variables)                → Unmapped — internal tooling 🟡
```

---

### Findings

#### FVA-01 | High | Structure | Tier mixing in component and chart collections

**Evidence:** The Components collection contains both semantic-tier references and component-scoped aliases in the same collection. The Chart collection contains variables named with semantic patterns (`chart-1` through `chart-5`) but holding raw `oklch()` values.

**Remediation:** Split the Components collection into a Semantic tier and a Component tier. Move chart variables to the Primitive collection and create semantic aliases (`color.data.series-1` etc.) in the Semantic collection.

---

#### FVA-02 | High | Structure | Component variables bypass semantic tier

**Evidence:** Several component variables reference primitive tokens directly (e.g. `button.bg.default → Primitives.blue.500`) rather than routing through the semantic tier (`button.bg.default → Semantic.color.action.primary → Primitives.blue.500`).

**Remediation:** Audit all component → primitive direct references. Create semantic intermediaries for each case, then update component aliases to route through them.

---

#### FVA-03 | High | Naming | `unofficial/` namespace defers architectural decisions

**Evidence:** 26 variables in the `unofficial/` namespace. Names include interaction states, feedback colours, and layout values that belong in the semantic tier.

**Remediation:** For each `unofficial/` variable: (a) promote to a stable semantic name, (b) delete if unused, or (c) document explicitly as a deliberate exception. The namespace should not exist in a stable architecture.

---

#### FVA-04 | High | Coverage | `general/destructive` has wrong value in shadcn mode

**Evidence:** `general/destructive` in the `shadcn` mode holds a hardcoded colour value instead of the correct primitive alias. The `shadcn-dark` mode is correct.

**Remediation:** Update `general/destructive` in the `shadcn` mode to alias the correct red primitive. Verify resolved value matches the shadcn/ui design intent.

---

#### FVA-05 | Medium | Naming | Semantic tokens use appearance-based names

**Evidence:** Several tokens in the semantic tier describe colour appearance rather than intent (e.g. tokens that include colour names in the semantic namespace).

**Remediation:** Rename to describe role: `color.feedback.destructive`, `color.action.primary`, etc. Reserve colour names for the primitive tier only.

---

#### FVA-06 | Medium | Coverage | No semantic feedback tokens for success/warning/info states

**Evidence:** The semantic tier has `destructive` but no `success`, `warning`, or `info` equivalents. Components that need these states reference unofficial variables or hardcode values.

**Remediation:** Add `color.feedback.success`, `color.feedback.warning`, `color.feedback.info` to the semantic tier with values in all three modes.

---

#### FVA-07 | Medium | Coverage | 8 semantic tokens hold hardcoded alpha values

**Evidence:** 8 variables in the semantic tier hold `rgba()` or `oklch()` with alpha channels as hardcoded values rather than referencing primitives with opacity modifiers.

**Remediation:** Add opacity-variant primitives (e.g. `Primitives.blue.500/20`) and update semantic aliases to reference them.

---

#### FVA-08 | Low | Naming | Comma decimal notation in spacing primitives

**Evidence:** Spacing primitives use comma-separated decimal notation (e.g. `0,5` instead of `0.5`) in some variable names or values.

**Remediation:** Standardise to period decimal notation. Use `figma_rename_variable` for name corrections; update values directly.

---

#### FVA-09 | Low | Structure | Docs and deprecated collections pollute the variable panel

**Evidence:** A Docs collection (internal tooling variables) and 2 deprecated variables appear in the variable panel alongside production tokens.

**Remediation:** Remove deprecated variables after confirming zero consumers. Move Docs variables to a hidden collection or prefix with `_` to signal internal use.

---

### Alias chain analysis

**Complete chain — passing:**
```
button.background.default
  → Semantic.color.action.primary  (1 hop)
    → Primitives.blue.500          (2 hops)
      → #0066CC                    (resolved)
Status: PASS — correct direction, 2 hops
```

**Incomplete chain — failing:**
```
chart-1 (Chart collection)
  → oklch(0.646 0.222 41.116)      (hardcoded, no alias)
Status: FAIL — primitive not referenced, theming cannot propagate
```

**Direct primitive reference — failing:**
```
[component variable].bg.default
  → Primitives.blue.500            (1 hop, skips semantic tier)
Status: FAIL — tier leakage, theming contract broken
```

---

### Mode coverage matrix

```
                    your_brand   shadcn   shadcn-dark
Primitives              ✓          ✓          ✓       (complete)
Semantic                ✓          ⚠          ✓       (1 gap: general/destructive wrong value in shadcn)
Components              ✓          ✓          ✓       (complete, via semantic)
Chart                   ✓          ✓          ✓       (complete, but hardcoded — not aliased)
Unofficial             partial    partial    partial   (inconsistent mode coverage)
```

---

### DTCG 2025.10 readiness

**Type declarations:** Figma variables don't natively support `$type`. Variable names partially encode types (`color.*`, `spacing.*`) but not consistently. A transformation layer would be required to export DTCG-compliant JSON.

**Composite types:** Text styles (14 found) and effect styles (10 found) cover typography and shadow composites, but these are Figma styles rather than DTCG-compatible composite tokens. Migration would require restructuring.

**Mode compatibility:** Mode names (`your_brand`, `shadcn`, `shadcn-dark`) are DTCG-compatible (no spaces, no slashes). Mode coverage gaps would need resolution before a clean DTCG export.

**Migration effort estimate:** 30–40 hours for a complete DTCG migration. Recommended sequence:
1. Fix structural issues (this audit's Fix First tier) — prerequisite
2. Standardise naming to encode DTCG types
3. Plan composite token structure for text/effect styles
4. Build a Style Dictionary or equivalent transformation layer

---

### Remediation priority

**Fix first — structural failures**
- FVA-04: `general/destructive` wrong value in shadcn mode (15 min)
- FVA-02: Component → primitive direct references (2–4 hours)
- FVA-01: Tier mixing in Collections/Chart (3–5 hours)

**Fix next — naming debt**
- FVA-03: Resolve `unofficial/` namespace (2–4 hours)
- FVA-05: Rename appearance-based semantic tokens (1–2 hours)
- FVA-06: Add missing feedback tokens (1 hour)
- FVA-07: Fix hardcoded alpha values (1–2 hours)

**Address eventually**
- FVA-08: Comma decimal notation (30 min)
- FVA-09: Clean deprecated and docs collections (30 min)
- DTCG migration (30–40 hours when ready)

---

---

## Token audit

**Skill:** `token-audit`
**Token source:** Figma variables only — no code token files found in `/Users/heidihayashitani/Development/design-system-ops/`
**Note:** Code token cross-reference skipped — no code token source configured in `.ds-ops-config.yml` and no token files found in codebase.

### Summary

The token system is Figma-native: 535 variables across 7 collections, with no parallel code token layer. The most critical structural failure is `general/destructive` carrying a hardcoded (and incorrect) value in the `shadcn` mode — this is a live defect. The next most urgent issue is the chart token block: 65 variables holding raw `oklch()` values mean an entire data visualisation domain is opaque to theming. The `unofficial/` namespace is the accumulation point for unresolved decisions and contains interaction states and feedback colours that belong in the semantic tier.

---

### Token inventory

| Collection | Variables | Tier | Notes |
|---|---|---|---|
| Primitives | ~180 | Primitive | Generally clean; spacing notation issue |
| Semantic | ~120 | Semantic | Core is sound; missing feedback states |
| Components | ~180 | Mixed | Semantic + component aliases in same collection |
| Chart | ~65 | Primitive (misnamed) | All hardcoded, no aliases |
| Unofficial | 26 | Unmapped | Deferred decisions |
| Deprecated | 2 | Unmapped | Never removed |
| Docs | ~12 | Internal | Pollutes production panel |
| **Total** | **535** | | |

**Modes:** `your_brand`, `shadcn`, `shadcn-dark`

---

### Findings

#### TA-01 | 🟠 High | Value | `general/destructive` hardcoded and wrong in shadcn mode

**Evidence:** `general/destructive` in the `shadcn` mode holds a raw colour value that does not match the shadcn/ui destructive colour specification. The `your_brand` and `shadcn-dark` modes correctly alias a primitive.

**Remediation:** Use `figma_update_variable` to set `general/destructive` in the `shadcn` mode to alias the correct red primitive. Verify resolved value.

**Effort:** 15 minutes. Can be fixed in place via Figma Console MCP.

---

#### TA-02 | 🟠 High | Value | ~65 chart tokens hold hardcoded `oklch()` values

**Evidence:** The Chart collection (`chart-1` through `chart-5` and variants) holds raw `oklch()` values in all three modes. No primitive aliases. A colour rebrand cannot propagate to data visualisation elements.

**Remediation:** Add chart colour primitives to the Primitives collection. Update chart variables to alias those primitives. Create semantic chart tokens (`color.data.series-1` etc.) in the Semantic collection and have chart variables reference semantics, not primitives directly.

**Effort:** 3–5 hours.

---

#### TA-03 | 🟠 High | Structure | 3 collections mix primitive and semantic tiers

**Evidence:** The Components collection contains both semantic-tier variables and component-scoped aliases. The Chart collection contains variables named semantically but containing raw values (tier confusion in both directions).

**Remediation:** Define strict collection roles. Split the Components collection into a dedicated Component tier collection. Reclassify Chart as Primitive and create Semantic chart aliases.

**Effort:** 3–5 hours.

---

#### TA-04 | 🟠 High | Naming | 26 `unofficial/` variables; 2 deprecated tokens never removed

**Evidence:** `unofficial/` is a namespace in production variables — visible to all Figma consumers. Contains: interaction states, feedback colours, layout variables. Two variables are prefixed with `--deprecated-` and have no known consumers.

**Remediation:** Audit each `unofficial/` variable for consumers. Promote to stable semantic names, or delete. Remove `--deprecated-` variables after confirming zero aliases point to them.

**Effort:** 2–4 hours to triage; 30 minutes to remove deprecated tokens.

---

#### TA-05 | 🟡 Medium | Value | 8 semantic tokens hold hardcoded alpha values

**Evidence:** 8 variables in the semantic tier contain `rgba()` or `oklch()` with explicit alpha channels rather than referencing primitives.

**Remediation:** Add opacity-variant primitives. Update semantic tokens to reference `{Primitives.color.X/opacity}` aliases.

**Effort:** 1–2 hours.

---

#### TA-06 | 🟡 Medium | Value | `semantic/rounded-none` holds hardcoded value

**Evidence:** `semantic/rounded-none` contains a hardcoded `0` rather than aliasing a radius primitive.

**Remediation:** Add `Primitives.radius.none: 0` and update `semantic/rounded-none` to alias it.

**Effort:** 15 minutes. Can be fixed in place.

---

#### TA-07 | 🟡 Medium | Structure | Interaction states buried in `unofficial/` namespace

**Evidence:** Hover, focus, active, and disabled state tokens exist in the `unofficial/` namespace rather than as part of the semantic tier (e.g. `unofficial/color.action.primary-hover` instead of `Semantic.color.action.primary-hover`).

**Remediation:** Promote interaction state tokens to the semantic tier with consistent naming: `color.action.primary.hover`, `color.action.primary.focus`, etc. Update all component aliases.

**Effort:** 2–3 hours.

---

#### TA-08 | 🟡 Medium | Coverage | No semantic feedback tokens for success/warning/info

**Evidence:** `color.feedback.destructive` exists in the semantic tier. No equivalents for `success`, `warning`, or `info`. Components using these states reference unofficial variables or hardcode values.

**Remediation:** Add `color.feedback.success`, `color.feedback.warning`, `color.feedback.info` to the semantic tier with values in all three modes.

**Effort:** 1 hour.

---

#### TA-09 | ⚪ Low | Naming | Comma decimal notation in spacing primitives

**Evidence:** Some spacing primitive names or values use comma notation (`0,5`) instead of period notation (`0.5`), inconsistent with the rest of the collection.

**Remediation:** Rename affected variables using `figma_rename_variable`. Can be fixed in place.

**Effort:** 30 minutes.

---

#### TA-10 | ⚪ Low | Structure | Docs tokens and empty collections pollute the variable panel

**Evidence:** A Docs collection containing internal tooling variables and an empty collection appear in the variable panel alongside production tokens. This creates noise for Figma consumers.

**Remediation:** Remove or hide Docs variables (prefix with `_` if needed for internal reference). Delete empty collection.

**Effort:** 15 minutes.

---

### Remediation priority

**Fix first** (1.5–2.5 hours total)
- TA-01: `general/destructive` wrong value — live defect, fix now (15 min)
- TA-04: Remove 2 deprecated tokens — quick win, zero-risk (30 min)
- TA-09: Comma decimal renames — low-risk, high-consistency value (30 min)
- TA-06: `semantic/rounded-none` hardcoded fix (15 min)

**Fix next** (8–14 hours total)
- TA-02: Chart token aliasing — large but contained (3–5 hrs)
- TA-03: Collection tier separation (3–5 hrs)
- TA-07: Promote interaction states from `unofficial/` (2–3 hrs)
- TA-05: Fix hardcoded alpha values (1–2 hrs)
- TA-08: Add missing feedback tokens (1 hr)

**Address eventually**
- TA-04: Full `unofficial/` namespace resolution (2–4 hrs)
- TA-10: Docs/empty collection cleanup (15 min)
- DTCG migration planning (when code token layer is introduced)

---

### DTCG 2025.10 assessment

**Current state:** No DTCG-format token files exist in the codebase. Figma is the sole token source. The variable structure uses Figma-native aliases rather than DTCG `$value` references.

**Migration readiness:** Low. Prerequisites before DTCG migration:
1. Resolve tier mixing (TA-03) — a mixed collection cannot produce clean DTCG files
2. Fix broken aliases (TA-01) — a bad alias exports a bad token
3. Resolve `unofficial/` (TA-04) — an unmapped namespace produces unmapped DTCG tokens

**Recommendation:** If a code token layer is planned, use Style Dictionary 4 with DTCG-format source files. The Figma variables would need a transformation step (via Variables REST API or Tokens Studio) to export as DTCG JSON. Estimate 30–40 hours for a clean migration after structural issues are resolved.

---

### Fix-in-place availability

The Figma Console MCP (Southleft) is connected. The following findings can be fixed directly in this session:

| Finding | Action | Tool |
|---|---|---|
| TA-01 | Update `general/destructive` value in shadcn mode | `figma_update_variable` |
| TA-04 (deprecated) | Remove 2 deprecated variables | Manual in Figma (destructive — confirm first) |
| TA-09 | Rename comma-decimal spacing primitives | `figma_rename_variable` |
| TA-06 | Update `semantic/rounded-none` to alias primitive | `figma_update_variable` |

To apply any of these fixes, confirm which findings to address and I'll proceed one category at a time.

---

---

> **A note on context:** This audit compares Figma variables against structural best practices — it does not see why variables were structured the way they are. Some findings may flag deliberate choices made during shadcn/ui adaptation. If any finding describes an intentional decision, let me know — I'll calibrate future audits to your team's conventions. The goal is to surface problems, not to second-guess decisions you've already made.
