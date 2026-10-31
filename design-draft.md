# Mechanical Expert
### Design Draft — Version 0.1
*LLM-native semantic memory for agent-driven legacy modernization*

## 1. Vision

The **Mechanical Expert** is a software tool that develops and maintains a shared understanding of a legacy system through the activities of AI agents.

Its ambition is to become the mechanical equivalent of experienced engineers who understood a legacy system after working with it for decades, but whose expertise is no longer available.

The Mechanical Expert does not attempt to document or reproduce the legacy system exhaustively. It aims to recover its essential business meaning, discover previously unrecognized relationships, identify accumulated accidental complexity, and preserve the understanding needed to create a simpler replacement.

The underlying modernization model is:

**Legacy system → Semantic compression → Mechanical Expert → Generative expansion → Modern system**

The Mechanical Expert's central asset is a persistent **semantic memory**, collaboratively developed by AI agents and maintained through LLM reasoning.

Its primary value is emergent discovery and knowledge distillation, not documentation completeness.

## 2. Scope and boundaries

The tooling focuses exclusively on collecting, maintaining, refining, and providing semantic knowledge derived from arbitrary agentic activities.

**In scope**

- Tight integration with agent harnesses such as Codex and Claude Code.
- Passive observation of agent activities through harness integration.
- Encouraging agents to contribute useful knowledge deliberately.
- LLM-based selection and distillation of observations.
- Persistent, Git-native semantic memory shared among team members.
- Cross-task reflection and discovery of previously unrecognized insights.
- Continuous consolidation, simplification, and removal of irrelevant knowledge.
- Contextual retrieval of memory for future agent activities.
- A small CLI for integration, interaction, and maintenance.

**Explicitly out of scope**

- Source-code parsing and static analysis.
- Runtime observation techniques and instrumentation.
- Extracting data from databases, documentation, or external systems.
- Providing tools for legacy-system exploration.
- Deterministic semantic inference engines or formal ontologies.
- Automatic source-code generation or transformation.
- Exhaustive requirements management or code-level traceability.
- Maintaining a synchronized digital twin of either the legacy or modern system.

The means by which an agent acquires information are deliberately unspecified. Any agent activity that produces potentially useful understanding may contribute to semantic memory.

## 3. Conceptual architecture

The architecture consists of a small set of cooperating components.

**Agent Harness → Activity Watcher → Knowledge Curator → Git-Native Semantic Memory ↔ Reflective Agent → Agent Context**

The memory is also directly accessible to ordinary coding agents through a CLI.

### 3.1 Agent Harness

An existing harness such as Codex or Claude Code executes agentic tasks.

The Mechanical Expert does not replace the harness or modify the agent's underlying reasoning process.

Integration uses two complementary mechanisms:

**Passive integration:** Lifecycle hooks or event streams report relevant activity, including task boundaries, tool usage, and completed work.

**Active integration:** Persistent agent instructions encourage the agent to consult and update semantic memory whenever it discovers something worth preserving.

Harness-specific adapters translate available lifecycle events into a small internal interface.

The integration must degrade gracefully when a particular harness does not expose a desired event.

### 3.2 Activity Watcher

The Activity Watcher accompanies ordinary agent tasks.

It maintains a lightweight, temporary record of relevant activity, providing context for later knowledge curation.

Its responsibilities include:

- Associating captured activity with a task or agent session.
- Receiving lifecycle events and selected tool results.
- Preserving useful contextual references.
- Accepting explicit knowledge contributions from the agent.
- Triggering knowledge curation at appropriate checkpoints.

The watcher does not attempt to reason about the legacy system itself.

It should neither interfere with task execution nor persist complete conversation transcripts as semantic memory. It also does not depend on access to an agent's hidden reasoning.

Captured activity is held temporarily and processed asynchronously where practical.

### 3.3 Knowledge Curator

The Knowledge Curator is an LLM-driven function that transforms captured activity and deliberate agent contributions into candidate knowledge.

Its central question is:

**What did this activity reveal that a future expert would benefit from knowing?**

The curator applies semantic judgment rather than formal extraction rules.

It should prefer knowledge that:

- Explains business behavior or intent.
- Reveals important relationships or hidden concepts.
- Identifies possible accidental complexity.
- Changes or challenges existing understanding.
- Exposes important gaps in knowledge.
- Would prevent future agents from repeating expensive exploration.

It should disregard routine implementation details, repetitive tool results, obvious code structure, and information unlikely to matter during modernization.

An empty contribution is a valid outcome.

The curator produces compact, self-contained knowledge notes, optionally referencing existing notes or relevant source artifacts.

### 3.4 Semantic Memory

Semantic Memory is the shared, persistent knowledge of the Mechanical Expert.

It is implemented as a collection of human- and LLM-readable text artifacts stored under Git version control.

It is not intended to be a conventional knowledge graph or normalized database.

The memory should resemble the conceptual notes of an experienced system engineer: concise explanations, relevant observations, conceptual relationships, unresolved questions, and interpretations of system behavior.

Natural language is the primary representation.

The metamodel provides only enough structure for discovery, retrieval, contextual interpretation, and collaborative maintenance.

### 3.5 Reflective Agent

The Reflective Agent periodically examines the growing semantic memory and performs open-ended LLM reasoning.

Unlike the task-level curator, it considers knowledge accumulated across multiple activities.

Its responsibilities are to:

- Connect discoveries from different investigations.
- Identify emerging business and architectural concepts.
- Recognize patterns and conceptual similarities.
- Detect apparent contradictions and obsolete assumptions.
- Develop broader explanations from fragmented observations.
- Identify likely historical or technical ballast.
- Merge related knowledge into more concise representations.
- Revise earlier interpretations.
- Remove knowledge that no longer contributes to useful understanding.
- Propose questions worthy of further investigation.

The reflective agent is explicitly authorized to change its mind.

Its objective is not to maximize the volume of knowledge but to improve its explanatory power and usefulness for modernization.

No deterministic semantic rules or truth-maintenance algorithm are required.

Reflection may be triggered by accumulated changes, completion of significant tasks, Git merges, or scheduled execution. Continuous reasoning does not require a permanently running LLM process.

### 3.6 Context Provider

The Context Provider makes semantic memory useful to arbitrary agents.

Given a task description or question, it assembles a compact selection of relevant knowledge.

It may use text search, metadata filtering, embeddings, and LLM-driven contextual selection. These are retrieval mechanisms, not deterministic architectural reasoning.

The resulting context should prioritize:

1. Relevant conceptual understanding.
2. Important existing insights.
3. Known uncertainties and caveats.
4. Supporting observations when needed.

The provider should avoid injecting the entire memory into an agent's context window.

Agents must also be able to explore memory interactively through the CLI.

## 4. Minimal semantic metamodel

The initial metamodel should remain deliberately small.

A single **Knowledge Note** is the fundamental artifact.

Each note contains natural-language content and minimal metadata.

Three primary kinds are sufficient:

| Kind | Meaning |
|---|---|
| Observation | Something learned or encountered during an activity |
| Insight | An interpretation, conceptual discovery, or synthesized understanding |
| Question | An unresolved issue that may improve understanding |

Additional kinds should be introduced only when experience demonstrates a concrete need.

Typical metadata includes a stable identifier, kind, topic, origin, optional references, and relevant source context.

The body is free-form Markdown.

### Example knowledge note

**File:** `.expert/inbox/session-241/settlement-boundary.md`

    ---
    id: mx-042
    kind: insight
    topics: [settlement, accounting]
    origin: MOD-241
    related: [mx-017, mx-036]
    ---

    # Settlement appears to be one business operation

    Three separate batch programs appear to
    implement stages of a single conceptual
    settlement process.

    Their intermediate files and sequential
    scheduling may be consequences of historical
    infrastructure limitations rather than
    essential business requirements.

    What seems essential:
    - Correct settlement balances
    - Prevention of duplicate postings
    - Reconciliation after failures

    Potential ballast:
    - Intermediate file handoffs
    - Fixed overnight execution windows

    Still uncertain:
    Whether downstream processes depend on
    settlement completing at a specific time.

The note is immediately understandable without a formal ontology.

It contains enough context to support future reasoning while leaving the LLM free to develop interpretations that were not anticipated by the metamodel.

## 5. Git-native persistence and collaboration

Git is the authoritative store for semantic memory.

All durable knowledge must be representable as merge-friendly, text-based artifacts.

A possible repository layout is:

    .expert/
        README.md
        prompts/
            curator.md
            reflector.md
        inbox/
            <session-id>/
                <note-id>.md
        memory/
            accounting.md
            settlement.md
            customer-management.md

Two knowledge areas serve different purposes.

**Contribution inbox**

Contains independently created, immutable knowledge contributions from agent sessions.

Unique paths allow concurrent agents to contribute without modifying the same files.

**Distilled memory**

Contains integrated, actively maintained conceptual knowledge.

The reflective agent may rewrite, consolidate, or remove these documents as understanding evolves.

Git history preserves previous interpretations without requiring the active memory to accumulate obsolete material indefinitely.

Changes to shared memory documents use normal Git review and merge practices. Competing edits may require reconciliation, but the ordinary contribution path should be largely conflict-free.

The following are not authoritative knowledge artifacts:

- Activity capture buffers.
- Raw agent transcripts.
- Search indexes.
- Embeddings.
- Generated summaries used only for retrieval.
- Other rebuildable caches.

These remain local or disposable.

A clean repository checkout must be sufficient to reconstruct the active semantic memory without relying on a central database.

## 6. CLI interface

The tooling exposes a small executable, provisionally named `mx`.

The CLI is the common interface between agents, watcher adapters, semantic memory, and reflective processes.

### Context and knowledge

- `mx context "<task>"` — Retrieve relevant memory for a task.
- `mx search "<query>"` — Search existing notes.
- `mx show <id>` — Read a knowledge note.
- `mx remember` — Submit a deliberate knowledge contribution.
- `mx question` — Record an unresolved question.

### Agent activity

- `mx watch start` — Associate a watcher session with the active task.
- `mx watch checkpoint` — Process buffered activity into candidate knowledge.
- `mx watch finish` — Finalize a task's knowledge contributions.

### Reflection and maintenance

- `mx reflect --recent` — Reflect on recent contributions.
- `mx reflect --topic <topic>` — Reconsider a conceptual area.
- `mx reflect --all` — Perform broader memory consolidation.
- `mx diff` — Inspect proposed memory changes.
- `mx validate` — Check artifact integrity and structural consistency.

These are proposed commands, not existing implementations. Their final syntax is an implementation decision.

The CLI should support ordinary terminal output and structured machine-readable input/output.

No MCP server is required.

## 7. Harness integration strategy

Integration should combine automatic observation with lightweight behavioral instructions.

### Session start

When an agent task begins, the harness adapter can:

- Establish a watcher session.
- Identify the current working repository.
- Obtain relevant semantic context.
- Make selected knowledge available to the agent.

Only task-relevant memory should be supplied.

### During the task

The agent works normally with whichever tools and information sources are available.

The watcher captures relevant activity where supported.

The agent is encouraged to make explicit contributions when it discovers something particularly meaningful.

For example, it might call `mx remember` after realizing that several technical components represent one business process.

Explicit contributions should complement passive observation, not replace it.

### At task checkpoints

The Knowledge Curator reviews selected activity and contributions.

It may produce new observations, insights, or questions.

It should consolidate repetitive evidence and avoid creating notes merely because activity occurred.

### At task completion

A lightweight reflection asks the agent:

**What have you learned that another engineer would otherwise have to rediscover?**

The Knowledge Curator then finalizes its candidate notes, and the watcher releases temporary state.

Knowledge files become ordinary Git changes available for review and sharing.

### Across tasks

The Reflective Agent examines newly contributed notes in the context of existing memory.

It produces proposed edits to distilled memory, discovers cross-task relationships, and may identify further investigation opportunities.

Its output is a Git diff, not an opaque database update.

Future agents benefit from this revised memory.

## 8. Prompting strategy

Prompting is the primary mechanism controlling semantic judgment.

Three distinct prompts should be maintained as versioned project artifacts.

### Agent participation prompt

Encourage the working agent to treat semantic memory as a shared resource.

The agent should consult relevant memory when useful and contribute discoveries that would matter to future investigations or modernization decisions.

Knowledge maintenance must remain secondary to its assigned task.

An agent should not be pressured to invent findings or contribute trivial observations.

### Curator prompt

Instruct the curator to behave like a knowledgeable engineer deciding what to preserve for a successor.

It should identify meaningful observations, conceptual discoveries, and unresolved questions.

It should reject low-value technical detail and avoid duplicating existing knowledge.

### Reflector prompt

Instruct the reflector to act as an experienced system expert reconsidering the accumulated understanding.

It should discover relationships, challenge previous conclusions, identify accidental complexity, and strive for a simpler conceptual representation.

It should prefer consolidation and reinterpretation over endless accumulation.

Uncertainty should remain explicit where it matters.

The prompts should define goals and judgment criteria, not prescribe deterministic reasoning procedures.

## 9. Knowledge distillation and forgetting

Semantic memory must not become another legacy system burdened by years of accumulated information.

The reflective process should continually optimize for usefulness and conceptual compactness.

Its maintenance operations include:

**Add:** Preserve genuinely new understanding.

**Connect:** Relate independent observations and discover broader concepts.

**Refine:** Improve an existing interpretation.

**Consolidate:** Replace fragmented notes with a more powerful explanation.

**Challenge:** Reconsider a conclusion using new information.

**Forget:** Remove irrelevant, redundant, or obsolete content from active memory.

Forgetting is an essential capability, not a failure of knowledge management.

However, uncertain but consequential behavior should not be discarded merely because it appears unusual or obsolete. The expert should distinguish suspected ballast from sufficiently understood ballast.

Git history provides a recovery mechanism for previously retained knowledge.

## 10. Non-functional requirements

The initial implementation should satisfy the following properties.

| Property | Requirement |
|---|---|
| Harness independence | Support multiple coding-agent harnesses through adapters |
| Minimal interference | Knowledge capture must not disrupt ordinary agent work |
| Local-first operation | Core capabilities work without a central service |
| Collaboration | Durable artifacts merge through standard Git workflows |
| Simplicity | Small metamodel and limited CLI surface |
| LLM-native reasoning | Semantic interpretation and discovery remain model-driven |
| Inspectability | Knowledge consists primarily of readable Markdown |
| Recoverability | Git retains historical versions of curated memory |
| Compactness | Memory maintenance favors meaning over accumulation |
| Security | Tool output is treated as untrusted data; secrets and sensitive transcripts are not persisted by default |

The watcher should not make successful coding tasks depend on a successful LLM curation pass.

Failed reflection runs should leave the existing memory unchanged.

## 11. Initial implementation

A first prototype should validate the idea with the smallest possible architecture.

**Phase 1 — Agent memory integration**

Build the CLI, minimal note format, Git repository conventions, and one harness adapter.

Use repository instructions and lifecycle hooks to capture meaningful activity and encourage explicit contributions.

**Phase 2 — LLM knowledge curation**

Add task-level curation that converts captured activity into useful Markdown notes.

Evaluate whether the curator preserves significant understanding without excessive noise.

**Phase 3 — Reflective mechanical expert**

Add the independent reflective agent.

Allow it to combine knowledge from multiple tasks, discover new concepts, simplify existing notes, and propose Git changes.

**Phase 4 — Contextual reuse**

Provide task-sensitive retrieval of distilled knowledge to new agents.

Evaluate whether this reduces repeated exploration and improves the agents' ability to understand the system.

A service architecture, remote database, dedicated graph store, or sophisticated workflow engine is not required for the prototype.

## 12. Success criteria

The prototype should demonstrate that:

1. Ordinary agentic tasks contribute useful knowledge without requiring extensive manual bookkeeping.
2. Independent agents can contribute to a shared memory through normal Git operations.
3. A reflective LLM can discover meaningful relationships that individual tasks did not explicitly report.
4. The reflective process can reduce and improve existing memory rather than merely add notes.
5. A new agent can use distilled memory to understand an unfamiliar system substantially faster than starting from source materials alone.
6. The accumulated understanding helps distinguish essential business behavior from historical implementation complexity.

The ultimate measure is not the number of collected observations or notes.

It is whether the Mechanical Expert develops an increasingly useful and compact understanding of the system.

## 13. Guiding principle

**The Mechanical Expert is not a documentation generator, a knowledge graph, or a deterministic expert system.**

It is an LLM-powered, continuously evolving collective memory that benefits from arbitrary agentic activities.

The watcher gives the expert opportunities to learn. The curator decides what deserves to be remembered. The reflector discovers meaning, changes its understanding, and removes unnecessary knowledge. The semantic memory allows that understanding to survive individual tasks and be shared among team members.

The intended outcome is not a complete representation of the legacy system.

**It is the smallest useful body of understanding from which intelligent agents can construct a better system.**
