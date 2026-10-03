# Kith PRD — Addendum

Supporting depth that informs [[prd]] but does not belong in its main narrative.

## A1. Competitive landscape (research digest, 2026-10-02)

Source: web research subagent; GitHub stats via `gh api` on 2026-10-02. Items marked (unverified) were not confirmed against primary docs.

| Name | OSS | Self-host | Email ingest | People/org views | Graph | Multi-tenant | License |
|---|---|---|---|---|---|---|---|
| Khoj | Yes | Yes | No | No | No | Multi-user (unverified) | AGPL-3.0 |
| AnythingLLM | Yes | Yes | Not native | No | No | Limited | MIT |
| Open WebUI Knowledge | Source-available | Yes | No | No | No | RBAC | BSD-3 + branding |
| Onyx (ex-Danswer) | Open core | Yes | Gmail, plain text only | No | No | Yes | MIT + proprietary ee |
| Supermemory | Yes (SaaS-first) | Partial | Gmail connector | No | Memory graph | Per-user API | MIT |
| Mem0 / OpenMemory | Yes | Yes | No | Partial | Optional | Per-user | Apache-2.0 |
| Graphiti / Zep | Framework | Yes | No (episodes) | Temporal entities | Temporal KG | group_id | Apache-2.0 |
| GraphRAG / LightRAG / Cognee | Libraries | Yes | No | Entities | Yes | No / datasets | MIT / Apache-2.0 |
| Screenpipe | Source-available | Desktop | Screen capture only | No | No | No | Commercial |
| Inbox Zero | Yes | Yes | Gmail/Outlook (triage) | No | No | Yes | (unverified) |
| Gemini in Gmail, Shortwave, Superhuman, Notion Mail | No | No | Yes | Minimal | No | Teams | Proprietary |
| Dex, Folk, Mesh (ex-Clay), Affinity | No | No | Metadata | **People pages, timelines, follow-ups** | Network view | Teams | Proprietary |
| Limitless/Rewind | No | No | No | No | No | No | Acquired by Meta Dec 2025, wound down |

Prototype-level OSS in the same direction (not products): gauravsurtani/Email-Link (email → Neo4j + agent), dhruvbansal333/email_project (commitment extraction), mboxer.

### Positioning gaps

1. Nobody combines self-hosted + email-native (threads, people) + entity graph. OSS Gmail ingesters (Onyx, Supermemory, Inbox Zero) treat mail as flat chunks or triage items.
2. Graph engines (Graphiti, GraphRAG, LightRAG, Cognee) ship no product UI for people, timelines or commitments.
3. Relationship intelligence (people pages, timelines, follow-ups) is SaaS-only and mostly metadata-based.
4. Local-first commercial options are retreating (Limitless → Meta, Screenpipe → commercial license, Khoj focus shift), leaving room for AGPL + local LLM + OIDC + multi-tenant.
5. MCP exists elsewhere but without email/entity semantics.
6. Joining mail, Paperless documents and chats on shared people/org entities has no OSS equivalent found.

### User needs observed

1. Privacy: refusal to upload large archives to SaaS; lock-in fear (Limitless shutdown).
2. Trustworthy answers: hallucination is the top complaint about Gemini in Workspace, so citations are mandatory.
3. Signal over noise: bulk filtering before extraction (inferred from product design).
4. Follow-ups and commitments: "who owes what, by when" (validated by Dex/Folk).
5. Indexing cost on modest hardware: incremental, resumable, low-priority extraction (inferred).

Sources: docs.onyx.app (Gmail connector), supermemory.ai/docs, blog.google (Gmail Gemini era), getdex.com, folk.app, betanews.com (Meta/Limitless), github.com/getzep/graphiti, github.com/gauravsurtani/Email-Link, github.com/dhruvbansal333/email_project.

## A2. Pilot evidence

From the author's private pilot write-up (pipeline, measurements, evaluation, lessons; not published because it contains personal mail). Key numbers: 5.3 s/thread extraction on Qwen3.6-35B-A3B; hit@5 of 97% vs 77% for the vector baseline; 28/30 answers correct. Known defects: forwarded mail content lost (69/86), missing Sent mail, topic sprawl, top-k cap on aggregate questions.

## A3. Model candidates to benchmark (architecture input)

Not requirements; candidates for the calibration set and the first recommendation list (FR-39). Names to be verified against current releases at architecture time.

| Tier (indicative) | Candidates | Notes |
|---|---|---|
| Small (CPU / NPU / ≤ 8 GB VRAM) | Qwen3 4B, Gemma 3/4 small variants, Phi-4-mini, Llama 3.2 3B (already on the ai node NPU) | Classification and simple per-field extraction; answering with heavy retrieval support |
| Medium (8–16 GB VRAM) | Qwen3 8B, Gemma ~12B class | Single-pass extraction with a reduced schema |
| Large (24 GB+ / unified memory) | Qwen3.6-35B-A3B (pilot), Gemma 4 26B-A4B | Full single-pass extraction, best answers |
| Embeddings / rerank | mxbai-embed-large, Qwen3-Reranker-0.6B (pilot) | Small and fast on any tier |

The pilot measured only the large tier (5.3 s/thread). Small-model extraction quality is unmeasured and is the main risk behind FR-42 and SM-7.
