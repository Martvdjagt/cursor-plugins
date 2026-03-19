---
name: continuous-documentation
description: Incrementally update the repository README.md by combining actual code changes (what) with conversation transcripts (why). Use when the hook triggers or the user asks to update documentation.
---

# Continuous Documentation

Keep the repository `readme.md` current using two sources of truth:
- **Git** tells you what changed.
- **Transcripts** tell you why it changed.

Both matter equally. This skill is self-contained — all documentation guidelines are embedded below.

## Core Principles

### 1. What and Why are equally important

"What" lives in the code — new files, changed signatures, added endpoints, altered domain logic. Use `git log` and `git diff` to find it.

"Why" lives in the conversation — the user's stated reasoning, rejected alternatives, constraints that shaped the decision. Use transcripts to find it.

A README entry that states what changed without why is a changelog line. A README entry that states why without what is an orphaned rationale. Neither is useful alone.

### 2. What NOT to include is equally important as what to include

Every section, sentence, and word must earn its place. If you cannot justify why a reader needs it, delete it. See the slop filter and exclusion rules below — they carry the same weight as the inclusion rules.

---

## Inputs

- **Git history**: `git log` and `git diff` since the last indexed commit (stored in incremental index).
- **Transcripts**: `~/.cursor/projects/<workspace-slug>/agent-transcripts/`
- **Existing readme**: `readme.md` (root for microservices, project-level for monoliths)
- **Incremental index**: `.cursor/hooks/state/continuous-documentation-index.json`

## Workflow

1. **Read** the existing `readme.md`.
2. **Determine project type** (Service, Monolith, UI, NuGet/NPM Package) from repository structure. Apply the matching README structure below.
3. **Load** incremental index if present.
4. **Gather what changed** (the What):
   - Run `git log --oneline` from the last indexed commit to HEAD.
   - For commits that touch documentation-relevant areas (new projects, changed domain/application layers, added endpoints, altered configuration), run `git diff` to understand the scope.
   - Identify: new capabilities, changed behavior, removed functionality, new integration points, architectural shifts, non-trivial business logic changes.
5. **Gather why it changed** (the Why):
   - Discover transcript files and process only new or changed ones (mtime newer than indexed).
   - From each transcript, extract stated reasoning: user-provided context, rejected alternatives, constraints, trade-offs, problem statements that prompted the change.
   - Correlate transcript intent with the git changes from step 4. Match by timeframe and subject matter.
6. **Merge What + Why** into README updates:
   - For each documentation-worthy change, write what it is and why it was done.
   - If the transcript provides no intent for a change, document the what only. Do not invent a why.
   - If the transcript reveals intent but the code change is trivial, skip it — intent without a meaningful what is noise.
7. **Update `readme.md`**:
   - Modify existing sections in place when content has changed.
   - Add new sections only when the structure calls for them.
   - Remove content that is no longer accurate.
   - Preserve the section structure for the detected project type.
8. **Run the slop filter** on every sentence (see below).
9. **Write back** the incremental index:
   - Store latest commit SHA processed.
   - Store latest transcript mtimes.
   - Remove entries for transcripts that no longer exist.

---

## README Structure by Project Type

A `readme.md` must exist at the repository root level and contain substantive information about the repository's purpose. Placeholder or auto-generated READMEs are not acceptable.

**Update policy:**
Only update when there are meaningful feature changes (new capabilities, changed behavior, non-trivial design shifts). Do not log routine refactors or obvious details.

For microservices the readme lives at root level. For monolith services the readme lives at project level.

### Service

**Purpose of the Service**
Short executive summary of what the service does.

**Position in the Landscape**
Mermaid sequence diagram showing how the various components in the repository interact.

**Design Choices**
Document only decisions that differ from conventional expectations or introduce significant trade-offs. When documenting a decision, explain why this choice was made over alternatives.

**Business Logic**
Focus on the application/domain layer where logic is more complex than simple CRUD. Highlight business rules, workflows, decision-making processes that involve conditions, transformations, state management, or dependencies. Do not document trivial logic.

### Monolith

**Purpose of the Monolith**
Short executive summary of what the monolith does.

**Position in the Landscape**
Mermaid sequence diagram showing how the various components in the repository interact. References to other `readme.md` files in the repository.

### UI

**Purpose & Key Flows**
Short description of what the UI enables and its main user flows.

**Architecture & State Management**
Routing, state management approach, and meaningful cross-cutting concerns (feature flags, offline support, caching).

**Design System & Accessibility**
Reference the design system or component library used and notable accessibility considerations.

**Integration & Data Handling**
Link to backend APIs or contracts. Highlight significant client-side validations or data transformations.

**Error & State Handling**
Document only non-trivial states (empty, loading, offline) and overall error-handling approach.

**Performance & Platform Support**
Only notable performance considerations and non-standard platform/browser support.

### NuGet Package / NPM Package

**Purpose & Fit**
Short summary of what the package does and when to use it.

**Installation & Compatibility**
Installation command, supported frameworks, notable dependencies.

**Public API Overview**
List entry points or abstractions, linking to API docs if available.

**Usage Examples**
One minimal and one advanced example demonstrating meaningful use.

**Configuration & Extensibility**
Configuration options and extension points that change core behavior.

**Versioning & Diagnostics**
Semantic versioning policy, compatibility constraints, logging or diagnostic options if relevant.

**Security & Licensing**
Security considerations and the package license.

### Non-Functional Requirements (ISO 25010)

Document NFRs only when they significantly impact design, operations, or user experience. Focus on measurable qualities that differ from default expectations.

- **Performance Efficiency** — Response time targets, throughput requirements, resource constraints.
- **Reliability** — Uptime/availability targets, RTO/RPO, fault tolerance mechanisms.
- **Security** — Auth approach, encryption, vulnerability management.
- **Maintainability** — Coverage targets (if meaningful), tech debt tracking, deployment/rollback procedures.
- **Scalability** — Horizontal/vertical strategy, load balancing, database scaling.
- **Compatibility** — Backward/forward guarantees, API versioning, platform support matrix.

---

## What Belongs in Documentation

- Purpose: what the service/project does, stated once and plainly.
- How components interact: sequence diagrams, dependency flow.
- Design decisions that would surprise a new reader or that deviate from convention. State the decision and why it was chosen over alternatives.
- Business logic that goes beyond CRUD — rules, workflows, state machines, conditional paths.
- Intent: why something was built this way. What problem prompted it. What constraint shaped it.

## What Does NOT Belong in Documentation

This list carries the same weight as the one above. Enforce it.

- Restatements of what the code already says. If the function is `CalculateShippingCost`, do not document "calculates the shipping cost."
- Implementation details that change frequently (file paths, class names, method signatures) unless they are public API surface.
- Setup instructions that belong in a wiki or onboarding guide, not a README.
- Auto-generated changelogs or version histories. The README is not a changelog.
- Anything that only matters during a single sprint or PR cycle.
- Internal ticket numbers, branch names, or developer names.
- A "what" without substance. If the change is a routine refactor or rename, it does not belong in the README regardless of how many files it touched.
- A "why" without a corresponding meaningful "what". Intent behind trivial changes is still trivial.

## Slop Filter

Every sentence in the output must pass this filter. Remove or rewrite any sentence that contains:

**Banned words and phrases:**
- "comprehensive", "robust", "seamless", "cutting-edge", "state-of-the-art"
- "leverage", "utilize" (use "use")
- "facilitate", "streamline"
- "This ensures", "This allows for", "This provides"
- "It's worth noting", "It should be noted"
- "In order to" (use "to")
- "As part of this change"
- "Going forward"
- "Powerful", "flexible", "scalable" (unless backed by a specific metric)
- "Best-in-class", "world-class", "enterprise-grade"
- "Boilerplate"
- "Out of the box"
- "End-to-end"
- "Key", "critical", "crucial" used as filler adjectives

**Structural slop:**
- Bullet points that just rephrase the heading.
- Sections with a single obvious sentence and no real content.
- Introductory paragraphs that say "This section describes X" — just describe X.
- Conclusions or summaries that repeat what was already said.
- Multiple sentences that say the same thing with different words.

**Tone slop:**
- Marketing language. The README is for developers, not a sales page.
- Excessive enthusiasm. State facts.
- Hedging without substance ("may potentially help improve").
- Passive voice used to avoid stating who does what.

**Test:** Read each sentence and ask: does this add information a developer cannot get from reading the code or the section heading? If no, delete it.

## Capturing Intent

When transcripts reveal why a change was made, distill it to a sentence or two in the relevant README section — placed next to the description of what changed. Good intent documentation reads like:

- "Uses event sourcing instead of direct DB writes because order state must be auditable across services."
- "Retry policy caps at 3 attempts with exponential backoff — chosen after observing transient Azure Service Bus timeouts under load."
- "Split into two endpoints rather than one polymorphic endpoint to keep OpenAPI schema readable for external consumers."

Bad intent documentation reads like:

- "This was implemented to improve the overall quality of the system."
- "This change ensures better performance and reliability."
- "The decision was made after careful consideration of various factors."

If the transcript does not contain meaningful intent, do not invent it. Silence is better than filler.

## Inclusion Bar

Update the README only when all of these are true:

- The change is user-facing, architecturally significant, or alters documented behavior.
- The change is committed (not speculative or abandoned).
- The existing README does not already accurately describe the current state.

## Exclusions

Never add to the README:

- Secrets, tokens, credentials, connection strings.
- Developer-specific local setup (use a CONTRIBUTING.md for that).
- Temporary workarounds with an expiry date.

## Incremental Index Format

```json
{
  "version": 1,
  "lastCommitSha": "abc123...",
  "transcripts": {
    "/abs/path/to/file.jsonl": {
      "mtimeMs": 1730000000000,
      "lastProcessedAt": "2026-03-19T12:00:00.000Z"
    }
  }
}
```
