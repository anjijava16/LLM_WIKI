# LLM_WIKI
An LLM Wiki is an architectural design pattern, nlike traditional Retrieval-Augmented Generation (RAG)—which searches unorganized raw files every time you ask a question—an LLM Wiki continuously compiles, updates, cross-references, and logs incoming sources into structured Markdown pages


# References
1. https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f


# What an "LLM wiki" is

A directory of markdown files with two halves that never mix:

raw/ — immutable copies of what you ingested. What was said.

wiki/ — pages an LLM wrote from those copies. What you know.


```text
raw/          = evidence / source material
concepts/     = synthesized knowledge about ideas/topics
entities/     = synthesized knowledge about specific named things
topics/       = broader organized subject areas
references/   = authoritative/external references
datasets/     = large/native data
```

Below is the corrected **end-to-end Zero → Hero architecture**, including `concepts/` and `entities/`.

---

# 1. LLM Wiki — architecture

Think of the complete system as **six layers**:

```text
┌─────────────────────────────────────────────────────────────┐
│                    6. MODEL LAYER                           │
│                                                             │
│ Claude │ GPT │ Gemini │ other LLMs                          │
└───────────────────────────┬─────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────┐
│                    5. AGENT LAYER                            │
│                                                             │
│ Research Agent │ Query Agent │ Ingestion Agent              │
│ Compilation Agent │ Audit Agent │ Architecture Agent        │
└───────────────────────────┬─────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────┐
│                    4. SKILL / PLUGIN LAYER                  │
│                                                             │
│ Skills │ Commands │ Subagents │ MCP integrations             │
└───────────────────────────┬─────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────┐
│                    3. KNOWLEDGE LAYER                       │
│                                                             │
│ concepts/ │ entities/ │ topics/ │ references/               │
│ indexes   │ wikilinks │ metadata                            │
└───────────────────────────┬─────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────┐
│                    2. SOURCE LAYER                          │
│                                                             │
│ raw/ │ PDFs │ Markdown │ URLs │ repositories │ images       │
└───────────────────────────┬─────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────┐
│                    1. STORAGE LAYER                         │
│                                                             │
│ Filesystem │ Git │ S3 │ databases │ external datasets       │
└─────────────────────────────────────────────────────────────┘
```

The key correction is that **`concepts/` and `entities/` are different knowledge objects**.

---

# 2. Correct directory architecture

A much better conceptual structure is:

```text
llm-wiki/
│
├── wikis.json
├── _index.md
├── log.md
│
└── topics/
    │
    └── agentic-ai/
        │
        ├── inbox/
        │
        ├── raw/
        │
        │   ├── papers/
        │   ├── articles/
        │   ├── repositories/
        │   ├── pdfs/
        │   ├── images/
        │   └── notes/
        │
        ├── wiki/
        │
        │   ├── concepts/
        │   │   ├── agent.md
        │   │   ├── agentic-rag.md
        │   │   ├── retrieval.md
        │   │   ├── tool-use.md
        │   │   └── multi-agent-systems.md
        │   │
        │   ├── entities/
        │   │   ├── claude.md
        │   │   ├── openai.md
        │   │   ├── langgraph.md
        │   │   ├── mcp.md
        │   │   ├── opensearch.md
        │   │   └── postgres.md
        │   │
        │   ├── topics/
        │   │   ├── agentic-rag.md
        │   │   ├── agent-orchestration.md
        │   │   └── enterprise-ai.md
        │   │
        │   └── references/
        │       ├── mcp-specification.md
        │       ├── anthropic-docs.md
        │       └── openai-docs.md
        │
        ├── datasets/
        │
        ├── output/
        │
        ├── _index.md
        ├── config.md
        ├── schema.md
        └── log.md
```

**This is the structure you were asking about.**

The exact directory names and organization should ultimately be checked against the current repository version, but conceptually this is the correct knowledge separation you want to understand.

---

# 3. The MOST important distinction

This is where most people get confused.

Imagine you ingest:

> "LangGraph is a framework for building stateful, multi-agent applications."

You could create:

```text
entities/langgraph.md
```

because **LangGraph is a specific thing**.

But:

> "What is an agent graph?"

belongs under:

```text
concepts/agent-graph.md
```

because **agent graph is an idea/concept**.

And:

> "How do agent graphs fit into Agentic RAG?"

could belong under:

```text
topics/agentic-rag.md
```

because that is a broader subject.

Therefore:

```text
                KNOWLEDGE
                    │
        ┌───────────┼────────────┐
        │           │            │
        ▼           ▼            ▼
    CONCEPTS     ENTITIES      TOPICS
        │           │            │
        │           │            │
        ▼           ▼            ▼
      Ideas       Things       Subjects
```

---

# 4. `concepts/` — what belongs here?

A concept is an **idea, principle, technique, pattern, or abstract subject**.

Examples:

```text
concepts/
├── retrieval.md
├── embeddings.md
├── vector-search.md
├── reranking.md
├── agent.md
├── tool-calling.md
├── planning.md
├── memory.md
├── reflection.md
├── multi-agent.md
├── agentic-rag.md
├── knowledge-graph.md
└── context-engineering.md
```

For example:

```text
concepts/reranking.md
```

might contain:

```markdown
# Reranking

## Definition

Reranking is a second-stage retrieval process...

## Why it exists

...

## Architecture

Retriever
   ↓
Top 100 candidates
   ↓
Reranker
   ↓
Top 10
   ↓
LLM

## Algorithms

- Cross encoder
- LLM reranking
- ColBERT

## Tradeoffs

...

## Related concepts

[[retrieval]]
[[vector-search]]
[[embeddings]]

## Related entities

[[entities/cohere]]
[[entities/opensearch]]
```

So `concepts/` is your **knowledge about how things work**.

---

# 5. `entities/` — what belongs here?

Entities are **specific identifiable things**.

For example:

```text
entities/
├── claude.md
├── gpt.md
├── gemini.md
├── langchain.md
├── llamaindex.md
├── langgraph.md
├── mcp.md
├── opensearch.md
├── postgres.md
├── pgvector.md
└── aws.md
```

Each entity can have:

```text
Name
Type
Description
Vendor
Official URL
Versions
Capabilities
Limitations
Relationships
Sources
Examples
```

For example:

```markdown
# LangGraph

## Type

Agent orchestration framework

## Organization

LangChain

## Purpose

Build stateful agent workflows...

## Architecture

...

## Important concepts

[[concepts/agent]]
[[concepts/state-machine]]
[[concepts/multi-agent]]

## Related entities

[[entities/langchain]]
[[entities/openai]]

## Sources

...
```

---

# 6. Entity vs Concept example

This is the easiest way to understand it.

### Entity

```text
LangGraph
```

Specific technology.

```text
entities/langgraph.md
```

### Concept

```text
Stateful agent orchestration
```

General idea.

```text
concepts/stateful-agent-orchestration.md
```

### Topic

```text
Agent orchestration
```

Large subject area.

```text
topics/agent-orchestration.md
```

### Source

```text
LangGraph documentation
```

Original evidence.

```text
raw/repositories/langgraph-docs.md
```

Therefore:

```text
RAW
 │
 │ evidence
 ▼
ENTITY / CONCEPT
 │
 │ organization
 ▼
TOPIC
```

---

# 7. Complete ingestion flow

Now let's take a real example.

You give the system:

```text
langgraph.pdf
```

The complete pipeline should conceptually be:

```text
                    USER
                     │
                     ▼
              langgraph.pdf
                     │
                     ▼
                 INGEST
                     │
                     ▼
             ┌───────────────┐
             │ File analysis │
             └───────┬───────┘
                     │
       ┌─────────────┼─────────────┐
       │             │             │
       ▼             ▼             ▼
      Text         Images        Tables
       │             │             │
       └─────────────┼─────────────┘
                     ▼
              Provenance data
                     │
                     ▼
                    raw/
                     │
                     ▼
              SOURCE REGISTER
                     │
                     ▼
              COMPILATION AGENT
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
     Concepts      Entities      Topics
        │            │            │
        └────────────┼────────────┘
                     ▼
                  wiki/
                     │
                     ▼
                 INDEXING
                     │
                     ▼
                WIKILINKS
                     │
                     ▼
             KNOWLEDGE GRAPH
```

That is the complete lifecycle.

---

# 8. Why `raw/` is so important

Never confuse:

```text
raw/
```

with:

```text
wiki/
```

`raw/` is your **evidence layer**.

Suppose 5 sources say:

```text
Source A → LangGraph supports persistence
Source B → LangGraph supports checkpoints
Source C → LangGraph uses state
Source D → LangGraph supports human-in-the-loop
Source E → LangGraph supports durable execution
```

The raw layer preserves these sources.

Then the entity:

```text
entities/langgraph.md
```

synthesizes them.

Then:

```text
concepts/durable-agent-execution.md
```

extracts the general idea.

This gives you:

```text
Evidence
   ↓
Knowledge
   ↓
Generalization
```

---

# 9. Compilation is where the intelligence happens

This is probably the most important component after ingestion.

Imagine 100 documents.

You don't want 100 documents forever.

You want:

```text
100 Sources
     │
     ▼
Extract facts
     │
     ▼
Resolve entities
     │
     ▼
Identify concepts
     │
     ▼
Build relationships
     │
     ▼
Synthesize knowledge
     │
     ▼
Create/update wiki
```

For example:

```text
20 PDFs
15 GitHub repositories
30 webpages
10 research papers
25 Markdown documents
         │
         ▼
       100 sources
         │
         ▼
       20 entities
         │
         ▼
       35 concepts
         │
         ▼
       10 topics
         │
         ▼
      Knowledge graph
```

That's why this architecture becomes powerful.

---

# 10. Knowledge graph

Now your Markdown files become a graph.

Example:

```text
                 Agentic RAG
                     │
        ┌────────────┼────────────┐
        │            │            │
        ▼            ▼            ▼
     Retrieval      Agent        Memory
        │            │
        │            ▼
        │        LangGraph
        │            │
        ▼            ▼
    OpenSearch       MCP
        │            │
        ▼            ▼
    Vector Search   Tools
```

The links might look like:

```markdown
[[concepts/agent]]
[[concepts/retrieval]]
[[concepts/memory]]
[[entities/langgraph]]
[[entities/mcp]]
[[entities/opensearch]]
```

This creates relationships that an agent can traverse.

---

# 11. Querying becomes graph traversal + reasoning

Suppose you ask:

> "Should I use LangGraph or AutoGen for an Agentic RAG system?"

The agent can reason:

```text
QUESTION
   │
   ▼
Identify entities
   │
   ├── LangGraph
   └── AutoGen
   │
   ▼
Find concepts
   │
   ├── orchestration
   ├── state
   ├── multi-agent
   └── tool calling
   │
   ▼
Find related sources
   │
   ▼
Compare
   │
   ▼
LLM reasoning
   │
   ▼
ANSWER
```

That is much more explainable than:

```text
embedding(question)
      ↓
nearest 5 chunks
      ↓
LLM
```

---

# 12. Quick / Standard / Deep query

A good mental model is:

### Quick

```text
Question
 ↓
Index
 ↓
Relevant wiki pages
 ↓
Answer
```

Fast.

---

### Standard

```text
Question
 ↓
Index
 ↓
Concepts
 ↓
Entities
 ↓
Related wiki pages
 ↓
Relevant sources
 ↓
Answer
```

More reliable.

---

### Deep

```text
Question
       │
       ▼
   Wiki index
       │
       ├── concepts
       ├── entities
       ├── topics
       └── references
              │
              ▼
          Raw sources
              │
              ▼
          External research
              │
              ▼
          Cross-check
              │
              ▼
        Evidence synthesis
              │
              ▼
            Answer
```

That's the mode you want for research.

---

# 13. Where Claude comes in

Claude is **not the wiki**.

Think:

```text
LLM Wiki
    │
    │ files / knowledge
    ▼
Agent
    │
    │ instructions
    ▼
Claude
```

Claude can:

```text
READ
WRITE
SEARCH
ANALYZE
SYNTHESIZE
LINK
RESEARCH
AUDIT
```

The agent controls the workflow.

---

# 14. Where Skills come in

Suppose you create:

```text
skills/
```

with:

```text
skills/
├── wiki-ingestion/
│   └── SKILL.md
│
├── wiki-query/
│   └── SKILL.md
│
├── research/
│   └── SKILL.md
│
├── architecture-analysis/
│   └── SKILL.md
│
└── document-analysis/
    └── SKILL.md
```

Then:

```text
User
 │
 ▼
Claude
 │
 ▼
Determine task
 │
 ├── ingest → wiki-ingestion
 │
 ├── query → wiki-query
 │
 ├── research → research
 │
 └── architecture → architecture-analysis
```

Skill = **procedure**.

---

# 15. Agent vs Skill

This distinction is critical.

```text
SKILL

"How should this task be performed?"
```

while:

```text
AGENT

"Who is responsible for performing this task?"
```

For example:

```text
Research Agent
    │
    ├── Research Skill
    ├── Wiki Query Skill
    ├── Source Evaluation Skill
    └── Citation Skill
```

Another:

```text
Architecture Agent
    │
    ├── Repository Analysis Skill
    ├── Architecture Skill
    ├── Diagram Skill
    └── Documentation Skill
```

---

# 16. Plugins

Now package everything.

```text
agentic-ai-plugin/
│
├── plugin.json
│
├── agents/
│   ├── research-agent.md
│   └── architecture-agent.md
│
├── skills/
│   ├── research/
│   ├── wiki-query/
│   └── architecture/
│
├── commands/
│   ├── research.md
│   └── architecture.md
│
└── mcp/
    └── servers/
```

Then:

```text
PLUGIN
   │
   ├── Agents
   ├── Skills
   ├── Commands
   └── MCP
```

A plugin is essentially a **distribution/package boundary**.

---

# 17. MCP

MCP becomes the bridge to external systems.

For your environment:

```text
                    AGENT
                      │
             ┌────────┼────────┐
             │        │        │
             ▼        ▼        ▼
            Wiki     MCP     Web
                      │
          ┌───────────┼─────────────┐
          │           │             │
          ▼           ▼             ▼
       OpenSearch   PostgreSQL      S3
          │           │             │
          ▼           ▼             ▼
       Retrieval     Data         Documents
```

This is where your existing infrastructure fits beautifully.

---

# 18. Large files

Now let's answer your earlier question more precisely.

Don't think:

> "What's the maximum file size?"

Think:

> **What information should be materialized into the wiki versus referenced externally?**

For example:

### 50-page architecture document

Fine:

```text
PDF
 ↓
raw
 ↓
concepts
 ↓
entities
 ↓
wiki
```

### 10 GB dataset

Don't convert it into Markdown.

```text
10 GB dataset
      │
      ▼
S3 / database
      │
      ▼
dataset manifest
      │
      ▼
MCP/query tool
      │
      ▼
Agent
```

### 1 TB document corpus

```text
1 TB corpus
    │
    ▼
Object storage
    │
    ▼
Index/search system
    │
    ▼
MCP
    │
    ▼
Agent
    │
    ▼
LLM Wiki
```

The wiki stores the **knowledge about the corpus**, not necessarily the entire corpus.

---

# 19. Multimodal architecture

For images, diagrams, screenshots and PDFs, I'd design:

```text
                    INPUT
                      │
       ┌──────────────┼──────────────┐
       ▼              ▼              ▼
      PDF           Image          Video
       │              │              │
       ▼              ▼              ▼
   PDF parser      Vision LLM      Video parser
       │              │              │
       └──────────────┼──────────────┘
                      ▼
               Structured facts
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
      Text         Diagram       Metadata
        │             │             │
        └─────────────┼─────────────┘
                      ▼
                    raw/
                      │
                      ▼
                 Compilation
                      │
             ┌────────┼─────────┐
             ▼        ▼         ▼
          Concepts Entities   Topics
```

For architecture diagrams, I'd preserve both:

```text
architecture.png
```

and:

```text
architecture.mmd
```

because Mermaid gives you a machine-editable architecture representation.

---

# 20. End-to-end enterprise architecture

Now let's combine **your Agentic RAG architecture** with the wiki approach.

```text
                         USER
                           │
                           ▼
                    ┌────────────┐
                    │   AGENT    │
                    └─────┬──────┘
                          │
            ┌─────────────┼───────────────┐
            │             │               │
            ▼             ▼               ▼
         Wiki Skill     MCP             Web
            │             │
            ▼             │
       LLM Wiki           │
            │             │
    ┌───────┼───────┐     │
    │       │       │     │
    ▼       ▼       ▼     │
 concepts entities topics  │
    │       │       │      │
    └───────┼───────┘      │
            │              │
            ▼              │
          raw/             │
                           │
             ┌─────────────┼──────────────┐
             ▼             ▼              ▼
         OpenSearch     PostgreSQL        S3
             │             │              │
             └─────────────┼──────────────┘
                           │
                           ▼
                      Tool results
                           │
                           ▼
                          Agent
                           │
                           ▼
                          LLM
                           │
                           ▼
                         Answer
```

This gives you **two complementary knowledge systems**:

### LLM Wiki

Curated, explainable, human/agent-readable knowledge.

### Enterprise retrieval infrastructure

Large-scale, high-performance data retrieval.

---

# 21. The complete lifecycle

Here is the entire system in one flow:

```text
                     ┌──────────────┐
                     │ DATA SOURCES │
                     └──────┬───────┘
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
        PDFs             Websites          GitHub
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ▼
                        INGESTION
                            │
                            ▼
                           raw/
                            │
                            ▼
                     SOURCE ANALYSIS
                            │
              ┌─────────────┼─────────────┐
              ▼             ▼             ▼
          Entities       Concepts       Facts
              │             │             │
              └─────────────┼─────────────┘
                            ▼
                       COMPILATION
                            │
          ┌─────────────────┼──────────────────┐
          ▼                 ▼                  ▼
      entities/          concepts/          topics/
          │                 │                  │
          └─────────────────┼──────────────────┘
                            ▼
                       REFERENCES
                            │
                            ▼
                         INDEXING
                            │
                            ▼
                        WIKILINKS
                            │
                            ▼
                    KNOWLEDGE GRAPH
                            │
                            ▼
                          QUERY
                            │
                            ▼
                          AGENT
                            │
          ┌─────────────────┼─────────────────┐
          ▼                 ▼                 ▼
        Wiki               MCP              Web
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ▼
                           LLM
                            │
                            ▼
                         ANSWER
                            │
                            ▼
                    NEW KNOWLEDGE
                            │
                            ▼
                         WIKI
```

That final arrow is important:

```text
ANSWER
  ↓
NEW KNOWLEDGE
  ↓
WIKI
```

This is how the knowledge base **compounds**.

---

# 22. What I would build for your Zero → Hero README

I would make the documentation much deeper than my previous outline:

```text
llm-wiki-zero-to-hero/
│
├── README.md
│
├── 00-MENTAL-MODEL.md
│
├── 01-WHAT-IS-LLM-WIKI.md
│
├── 02-ARCHITECTURE.md
│
├── 03-DIRECTORY-STRUCTURE.md
│
├── 04-RAW-SOURCES.md
│
├── 05-CONCEPTS.md
│
├── 06-ENTITIES.md
│
├── 07-TOPICS.md
│
├── 08-REFERENCES.md
│
├── 09-DATASETS.md
│
├── 10-INGESTION.md
│
├── 11-MULTIMODAL-INGESTION.md
│
├── 12-PDF-INGESTION.md
│
├── 13-IMAGE-DIAGRAM-INGESTION.md
│
├── 14-COMPILATION.md
│
├── 15-WIKILINKS.md
│
├── 16-KNOWLEDGE-GRAPH.md
│
├── 17-INDEXING.md
│
├── 18-QUERY.md
│
├── 19-QUICK-QUERY.md
│
├── 20-STANDARD-QUERY.md
│
├── 21-DEEP-QUERY.md
│
├── 22-RESEARCH.md
│
├── 23-AGENTS.md
│
├── 24-SKILLS.md
│
├── 25-PLUGINS.md
│
├── 26-MCP.md
│
├── 27-CLAUDE.md
│
├── 28-CODEX.md
│
├── 29-MULTI-MODEL.md
│
├── 30-LARGE-FILES.md
│
├── 31-ENTERPRISE-SCALING.md
│
├── 32-RAG-INTEGRATION.md
│
├── 33-OPENSEARCH-INTEGRATION.md
│
├── 34-POSTGRES-INTEGRATION.md
│
├── 35-S3-INTEGRATION.md
│
├── 36-AGENTIC-RAG.md
│
├── 37-MULTI-AGENT-ARCHITECTURE.md
│
├── 38-SECURITY.md
│
├── 39-OBSERVABILITY.md
│
├── 40-PRODUCTION.md
│
├── 41-TROUBLESHOOTING.md
│
└── diagrams/
    │
    ├── overall.mmd
    ├── ingestion.mmd
    ├── compilation.mmd
    ├── concepts-entities.mmd
    ├── knowledge-graph.mmd
    ├── query.mmd
    ├── research.mmd
    ├── agent.mmd
    ├── skill.mmd
    ├── plugin.mmd
    ├── mcp.mmd
    └── enterprise-agentic-rag.mmd
```

---

# 23. One final mental model

If you're learning this for **Agentic AI architecture**, remember these six nouns:

```text
SOURCE
   ↓
RAW
   ↓
CONCEPT / ENTITY
   ↓
TOPIC
   ↓
KNOWLEDGE GRAPH
   ↓
AGENT
```

And these six technologies/layers:

```text
┌──────────────┐
│ LLM          │ ← reasoning
├──────────────┤
│ Agent        │ ← orchestration
├──────────────┤
│ Skills       │ ← procedures
├──────────────┤
│ Plugins/MCP  │ ← capabilities/integration
├──────────────┤
│ LLM Wiki     │ ← persistent knowledge
├──────────────┤
│ DB/S3/Search │ ← large-scale data
└──────────────┘
```

### The critical difference

**`raw/`** → *What did the source say?*

**`entities/`** → *What specific thing are we talking about?*

**`concepts/`** → *What general idea did we learn?*

**`topics/`** → *How do we organize a broader subject?*

**`references/`** → *Where is the authoritative information?*

**`datasets/`** → *Where is the large/native data?*

**Agent** → *What should I do with this knowledge?*

**Skill** → *How should I do it?*

**Plugin** → *How do I package/distribute those capabilities?*

**MCP** → *How do I connect the agent to external systems?*

**LLM** → *How do I reason over the available information?*

That is the **corrected end-to-end mental model** I would use as the foundation for your deep-dive documentation.
