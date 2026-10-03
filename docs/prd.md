---
title: Kith — Product Requirements Document
status: final
created: 2026-10-02
updated: 2026-10-03
product: Kith
repo: https://github.com/sixtoad/kith
license: AGPL-3.0
---

# PRD: Kith

## 0. Document Purpose

This PRD defines what Kith must do. It is the input for UX design, architecture and epics. It describes capabilities, not implementation: technology choices, the competitive landscape and pilot evidence live in [[addendum]]. Terms in §3 Glossary are used exactly as defined throughout. Inferred requirements carry inline `[ASSUMPTION]` tags, indexed in §10; §9 lists open questions. Requirement IDs are stable, not sequential: later additions (FR-43–45, NFR-11–12) sit in the section they belong to.

Inputs it builds on:

- Private research by the author: *Paperless-ngx as Document Hub* (2026-10-02) and *Self-hosted Email Archive + Graph RAG* (2026-10-02), including the mail-graph pilot (addendum A2)
- Competitive landscape digest (addendum A1)
- A decision log (D1–D25) kept in the author's private planning workspace

## 1. Vision

Kith is a self-hosted, indexed corpus of your life. It reads your correspondence and documents (mail first, then scanned and digital documents) and connects what they are about: the people, organisations, things you own, money, dates and promises. It can answer almost any question about your past, citing its sources. Example: "what is the story with my alarm?" brings together the AlarmCo emails, the installer's quote, the Visonic PowerMaster manual and every related invoice, whether it arrived by email or was scanned.

Kith's real value is **proactive**. It crosses incoming information with what it already knows and turns it into action. A contract clause saying the alarm must be serviced every three years becomes a calendar reminder to call the installer. A low-battery alert from Home Assistant arrives with the battery-change steps from the manual. Most of the time you never open Kith; it reaches you when something matters.

That only works if people trust it, so the UI is the **trust surface**. It shows that the Index is current and complete and that its answers are correct. It lets you check and fix what Kith extracted, and only then ask, search and explore. Kith is AGPL-3.0 and runs on modest self-hosted hardware with local Models. Each installation serves one household or one small company in the MVP, with several Members and private or shared Spaces; serving several Tenants from one installation follows in Phase 2.

## 2. Target User

### 2.1 Primary Personas

- **The household archivist (Alex).** Technical, self-hosts everything, already runs Paperless-ngx and Home Assistant. Has 7+ years of Gmail and a growing pile of scanned paperwork. Wants answers and reminders without handing an archive to a cloud vendor. Configures Kith and verifies it.
- **The household partner.** Non-technical. Wants to find the creche contract or the insurance renewal date without asking the archivist. Shares house-related knowledge; some of their mail is private. `[ASSUMPTION]` uses Kith mainly through the Ask and Search surfaces and notifications, never configuration.
- **The small-company organiser.** Runs or administers a small company (≈2–20 people). Has supplier contracts, invoices, customer and staff correspondence spread across mailboxes and folders. Needs one place that knows what was agreed with whom, what is owed and when renewals fall. `[ASSUMPTION]` has an admin who installs Kith, or uses an installation run by someone like the household archivist.

### 2.2 Jobs To Be Done

- **Remember for me:** answer "what did we agree / pay / receive, and when?" across years of mail and documents, with proof.
- **Connect it for me:** link a company, a thing I own, its manual, its contract and its invoices without manual filing.
- **Warn me in time:** surface obligations and renewals (service intervals, contract end dates, mortgage renewal) before they bite.
- **Help in the moment:** when something happens (a device alert, an incoming email), bring the relevant knowledge to it.
- **Let me trust it:** show me the Index is complete and current, and let me correct it.
- **Keep it mine:** private, self-hosted, local models, exportable.

### 2.3 Non-Users (v1)

- People wanting an email client (reading, replying, inbox triage). Kith never sends mail.
- Enterprises needing compliance archiving (legal hold, retention policies, audit-grade journaling).
- Users unwilling to run a self-hosted service. There is no hosted offering.

### 2.4 Key User Journeys

- **UJ-1. The archivist checks that Kith can be trusted before relying on it.**
  Alex has connected their Gmail account. Kith has been backfilling for two days. Alex opens the **Index Health** view and sees all-mail sync status (newest message 4 minutes old, backfill 82% done, ETA tomorrow), counts by Class (personal / transactional / bulk), extraction backlog and errors. They open a sample of bulk Messages to make sure no real correspondence was filtered out, reclassify one, and merge "Acme Recruitment Ltd" into "Acme Recruitment". They run their Golden Questions and see 19/20 pass with correct citations. **Climax:** Alex can see the Index is current and correct, not just take it on faith. **Edge case:** the Gmail credential stops working; the view shows the connector in error with the reason and last success time, and the Ask surface warns that answers may be stale.

- **UJ-2. The archivist asks for the full story of the alarm.**
  Alex asks "What is the history of my alarm system and what has it cost?". Kith answers with a timeline: AlarmCo contract and non-renewal, €85 app-fee offer, the installer's quote (€150 labour, €219 IP module), the visit on 28 Sep 2026, the installer's invoice (paid) and the Visonic PowerMaster 10 manual. Every claim cites its email, attachment or document. Totals come from the graph, not from the top few snippets. **Climax:** one answer replaces an hour of searching. **Edge case:** an invoice exists both as an email attachment and as a scan; it is listed once, with both sources shown.

- **UJ-3. The partner finds a document fast.**
  The partner searches "creche fees 2026". Results show the creche's Organisation page (fees, staff, key dates) and the latest fee letter. They open the Thread in the reader, then download the PDF. **Climax:** found in seconds, no help needed.

- **UJ-4. The small-company organiser lists what a supplier has billed.**
  The organiser opens the Organisation page for their accountant: all invoices (emailed and scanned) with amounts and dates, the engagement letter, open commitments ("send Q3 VAT figures by 15 Oct"), and the people involved. They export the invoice list. **Climax:** a complete, sourced list without reading mail.

- **UJ-5 (Phase 2). A contract obligation becomes a reminder.**
  Kith extracts from the AlarmCo contract that the alarm must be serviced every three years from installation. It proposes a calendar event "Call Dan Byrne — alarm service due" for the right date, linked to the contract clause, the Asset and the installer's contact. Alex approves it once; it appears in their calendar. **Edge case:** the contract is later replaced; Kith flags the reminder as possibly obsolete.

- **UJ-6 (Phase 2). A device alert arrives with the fix attached.**
  Home Assistant sends Kith a "low battery: backyard sensor" event. Kith matches the sensor to the Visonic PowerMaster 10 Asset and returns a notification enriched with the battery-change steps and battery type from the manual, plus who installed it. **Climax:** the fix arrives with the problem.

- **UJ-7. A life event drives a deep dive.**
  The mortgage renewal is due (every 5 years). Alex asks "Summarise my mortgage: lender, rate history, current terms, renewal date, documents". Kith answers from the original offer letter, annual statements and correspondence, with citations.

## 3. Glossary

- **Tenant** — An isolated Kith account (e.g. a household, a company). Owns its Sources, Items, Entities and settings. One per installation in the MVP; several in Phase 2. No data crosses Tenants.
- **Member** — A person with access to a Tenant, authenticated via OIDC. A Member has a role (owner, admin, member).
- **Space** — A visibility scope inside a Tenant: **private** (one Member) or **shared** (all Members). Every Item belongs to exactly one Space; derived Entities, Facts and merged Documents are visible according to the Items they come from (FR-3).
- **Source** — A configured origin of content, e.g. a Gmail account, an IMAP mailbox, a Paperless-ngx instance, a document upload folder. Belongs to one Member or to the Tenant.
- **Connector** — The capability that syncs a Source incrementally into Items.
- **Item** — One unit of ingested content: a **Message** (one email) or a **Document** (a file: PDF, image, office file, manual, scan). Items are immutable originals with metadata.
- **Thread** — An ordered conversation of Messages, using the provider's own thread identity when available.
- **Attachment** — A file inside a Message. Each Attachment becomes a Document linked to its Message.
- **Class** — Triage label of a Message: **personal**, **transactional** or **bulk**.
- **Entity** — A real-world thing Items are about: **Person**, **Organisation**, **Asset** (a thing owned: alarm system, boiler, car, property), **Place**, **Topic**. Entities are shared across Sources within a Space.
- **Fact** — A structured statement extracted from Items: **Event** (dated happening), **Commitment** (who owes what to whom, by when, status), **Amount** (money with currency and purpose), **Obligation** (recurring or dated duty stated in a contract or document, e.g. "service every 3 years").
- **Provenance** — The link from every Entity and Fact back to the Items (and passages) it came from.
- **Index** — All Items, Entities, Facts and their search structures for a Tenant.
- **Index Health** — The UI and API showing Index freshness, coverage, backlog, errors and quality.
- **Golden Question** — A Member-defined question with an expected answer and expected sources, used to test answer quality over time.
- **Answer** — A natural-language response from the Ask surface, made of claims, each with citations to Items.
- **Model** — A language, embedding or reranking model reached through an OpenAI-compatible endpoint. Kith uses Models per **Task** (classification, extraction, answering, embedding, reranking).
- **Hardware Tier** — A class of host capability (e.g. CPU-only, small GPU, large GPU or unified memory) that Kith maps to recommended Models.
- **Model Profile** — Kith's measured record of what a Model can do reliably (schema size, context, extraction accuracy, speed). Kith adapts its pipeline to the Model Profile.
- **Action** (Phase 2) — Something Kith proposes or performs outside itself: a calendar event, a notification, an enriched reply to an inbound event.
- **Trigger** (Phase 2) — An inbound event (new Item, Obligation coming due, external webhook such as Home Assistant) that can produce Actions.

## 4. Features

### 4.1 Tenancy and access

**Description:** In the MVP one installation serves one Tenant (a household or a small company); multi-tenancy is Phase 2 (FR-44). Members sign in with OIDC (e.g. Keycloak). Within a Tenant, each Member's personal Sources default to their private Space. Members can mark Entities or Items as shared (e.g. "the house", the mortgage). Realizes UJ-1, UJ-3, UJ-4.

#### FR-1: Tenant and Members
In the MVP an installation has exactly one Tenant. Its owner can invite Members and assign roles (owner, admin, member).
- All data carries its Tenant, so adding Tenants later (FR-44) needs no data migration.
- Removing a Member removes their private Sources, Items and derived private Facts; shared content stays.

#### FR-2: OIDC sign-in
Members sign in via an OIDC provider configured per installation.
- No local password database is required. `[ASSUMPTION]` one optional local admin account exists for bootstrap/recovery.

#### FR-3: Private and shared Spaces
Every Item, Entity and Fact belongs to a private or shared Space.
- An Answer for a Member only cites Items visible to that Member.
- A Member can share an Item, Entity or Thread to the Tenant's shared Space, and unshare it. Facts derived only from private Items stay private.
- `[ASSUMPTION]` Entities seen in both private and shared Items are shared; private Facts about them are not.
- A Document merged from several copies (FR-13) is visible to every Member who can see at least one of its copies; each copy keeps its own Space, and citations point to a copy the viewer can see.

#### FR-44: Multiple Tenants (Phase 2)
An installation admin can create several Tenants on one installation.
- A Member of one Tenant cannot see, search or receive Answers containing data from another Tenant, through any surface including MCP.
- Deleting a Tenant removes all its Items, Entities, Facts and credentials.

### 4.2 Sources and connectors

**Description:** Connectors sync Sources incrementally and resumably. A Source can be large (100k+ Messages, 7+ years), so sync starts with the newest content and backfills history over days within provider limits. Realizes UJ-1.

#### FR-4: Gmail connector (MVP)
A Member can connect a Gmail account and sync **all mail**, including Sent and excluding Spam and Trash.
- Captures per Message: raw original, provider thread id, labels and Gmail category (primary / promotions / social / updates / forums).
- New mail is **searchable** within 15 minutes of arrival and **extracted** (Entities and Facts available) within 24 hours. `[ASSUMPTION]`
- Backfill is resumable after interruption and respects provider download limits. A paused backfill is shown in Index Health with its reason.
- Credentials are stored encrypted and never shown back in the UI.
- Read-only: Kith never modifies, moves or flags anything in the mailbox.

#### FR-5: Document upload and watched folder (MVP)
A Member can add Documents by upload and by a watched folder.
- Supports PDF, images (with OCR), and common office formats. `[ASSUMPTION]` OCR covers English and Spanish.
- Lets a Member add the Visonic manual or a scanned invoice without Paperless.

#### FR-6: Paperless-ngx connector (Phase 2)
A Member can connect a Paperless-ngx instance; its documents, OCR text and metadata (correspondent, type, tags, custom fields) sync as Documents.
- Sync is incremental and resumable, and picks up updates and deletions in Paperless.
- Paperless tags, types and correspondents are mapped through Kith's controlled vocabulary (FR-19) and Entity resolution (FR-18), never imported as new Topics or Entities unreviewed.
- Paperless copies of emails already ingested from a mail Source are recognised as duplicates (FR-13).
- When Paperless fields disagree with Kith's extraction, both are kept and the disagreement is shown; a Member-confirmed value wins (FR-16).

#### FR-7: Generic IMAP connector (Phase 2)
A Member can connect any IMAP mailbox (e.g. Outlook/Hotmail, company mail) with the same guarantees as FR-4 except Gmail-specific labels and categories.

#### FR-8: Mailbox import (Phase 2)
A Member can import an mbox or Maildir export (e.g. Google Takeout, old accounts) as a one-off Source.

#### FR-9: Chat exports (Phase 3)
A Member can import chat exports (e.g. WhatsApp) as Items.

### 4.3 Ingestion and normalisation

**Description:** Raw Items become clean, connected content. The pilot showed that threading, forwards, quoting and bulk mail decide quality. Realizes UJ-1, UJ-2.

#### FR-10: Threads
Messages are grouped into Threads using the provider's thread identity when available, else reply headers.
- A reply whose parent is not in the Index still shows in its Thread with a placeholder.

#### FR-11: Forwarded and quoted content
- A forwarded Message keeps the forwarded content as content and records the original sender and date.
- Quoted history is removed from a Message's text only when the quoted Message exists in the Index. Otherwise it is kept, since it may be the only copy.

#### FR-12: Attachments become Documents
Each real Attachment becomes a Document linked to its Message and Thread, with the same Space as the Message.
- Inline images (logos, signatures, tracking pixels) and attachments of bulk Messages do not become Documents.

#### FR-13: Duplicate detection across routes
The same document arriving by different routes (email attachment, scan, Paperless) is recognised as one logical Document with several Provenance links.
- Exact duplicates are always merged. Near-duplicates (scan vs original PDF) are merged when content and key fields (issuer, number, date, amount) match. `[ASSUMPTION]`
- A Member can split a wrong merge.

#### FR-14: Message classification
Every Message gets a Class (personal / transactional / bulk).
- Provider categories are used when available. Header signals, sender reputation and "people I wrote to" are used otherwise.
- Mail sent by a Member, or to a Member by someone they have written to, is never bulk.
- Bulk Messages are kept and keyword-searchable but excluded from extraction and from Answers unless explicitly requested.

#### FR-45: Model-free correspondence graph
For every Message, Kith records without any Model: sender and recipients as Persons with their addresses, their Organisations by domain, Threads, Attachments and labels.
- Entity pages, "who did I write to about…" and per-correspondent lists work during backfill and before extraction, on any Hardware Tier.

### 4.4 Knowledge extraction and Entity resolution

**Description:** Kith extracts Entities and Facts from personal Messages, transactional Messages and Documents, and links them across Sources. Every Fact keeps Provenance. Realizes UJ-2, UJ-4, UJ-5, UJ-6.

#### FR-15: Entity and Fact extraction
For each Thread (personal and transactional) and each Document, Kith extracts Entities (Person, Organisation, Asset, Place, Topic) and Facts (Event, Commitment, Amount, Obligation), plus a summary.
- Every Entity and Fact links to the Items and passages it came from.
- Extraction runs incrementally on new Items and is resumable. Batch work runs at low priority so it does not starve interactive use.
- On the built-in reference set (FR-41), extraction recalls ≥ 85% of annotated Facts with ≥ 90% precision on the minimum Hardware Tier. `[ASSUMPTION]`

#### FR-16: Document understanding
Kith recognises document types needed for the core journeys: invoice/receipt, contract, statement, manual, letter. `[ASSUMPTION]`
- Invoices: issuer, number, date, total, currency, due date, IBAN or payment reference, paid status where stated. Embedded structured e-invoice data (e.g. Factur-X/ZUGFeRD, XRechnung) is used when present. Key fields are ≥ 95% correct on the reference set. `[ASSUMPTION]`
- Contracts: parties, start/end dates, renewal terms, Obligations.
- Manuals: the Asset they describe and procedure sections (e.g. battery replacement) retrievable by task.
- A Member can mark a Document's fields as **confirmed**; confirmed values win over any later extraction.

#### FR-17: Commitments with direction and status
A Commitment records who owes it, to whom, what, by when, and its status (open / done / cancelled / unknown).
- Later Items can change status (e.g. "paid", "done", "cancelled"); the change keeps Provenance.

#### FR-18: Entity resolution
Mentions of the same real-world Entity are merged across Items and Sources (name variants, legal suffixes, email addresses of one Person).
- Members can merge, split and rename Entities; manual decisions survive re-extraction.
- Wrong automatic merges stay below 2% on the reference set; when unsure, Kith proposes a merge instead of making it. `[ASSUMPTION]`

#### FR-19: Controlled topics
Topics come from a per-Tenant vocabulary. New Topics are proposed, not silently created, and a Member approves or maps them.

#### FR-20: Assets
Members and extraction can create Assets (e.g. "Alarm system — Visonic PowerMaster 10", "House — Main Street"). Documents, Organisations, Facts and external device ids (Phase 2) can link to an Asset.

### 4.5 Models and hardware adaptation

**Description:** Kith must run well on modest hardware, where efficient 4B–8B Models are the realistic choice, and take advantage of larger Models when they exist. It recommends Models for the host, measures what the configured Models can actually do, and adapts how it works to their strength instead of assuming one large Model. Realizes UJ-1 (trust includes knowing what the Model can do).

#### FR-39: Recommended models per Hardware Tier
Kith maintains and shows a list of recommended Models per Hardware Tier and Task, and suggests a configuration for the detected host.
- Recommendations are versioned with Kith releases and editable by the installation admin.
- The minimum supported Hardware Tier for the MVP is an 8B-class Model on a 16 GB GPU or unified-memory host (D21). Smaller (4B, CPU-only) Hardware Tiers are supported best-effort with stated limits.

#### FR-40: Per-task model assignment
An admin can assign a different Model to each Task (e.g. a small Model for classification, a mid-size Model for extraction, the largest available for answering).
- Each Task works with any OpenAI-compatible endpoint; no Task requires a specific vendor or model family.

#### FR-41: Model calibration
When a Model is configured or changed, Kith runs a built-in calibration set (structured extraction, classification and answer samples with known results) and stores a Model Profile.
- Index Health shows the Model per Task, its Model Profile and measured accuracy and speed.

#### FR-42: Adaptive pipeline
Kith adapts extraction and answering to the Model Profile, for example:
- Thresholds come from the Model Profile (FR-41): a Model below the extraction target for a Fact type gets the simpler strategy for it, or that Fact type is disabled with a visible notice.
- Weaker Models get smaller, simpler extraction steps (fewer fields per call, shorter context, more deterministic rules); stronger Models get richer single-pass extraction.
- Answering uses more retrieval and graph tools and less free reasoning on weaker Models.
- Facts carry the Model that produced them; a Member can re-extract with a stronger Model later, and corrections survive (FR-23).
- Quality limits of the current Models are stated, not hidden (e.g. "commitment extraction disabled: model below threshold").

### 4.6 Index Health and verification (trust surface)

**Description:** The surface that makes Kith believable. It answers "is the Index current?", "is it complete?" and "is it right?", and lets Members fix it. Realizes UJ-1.

#### FR-21: Freshness and coverage
For each Source: connection status, last successful sync, newest Item age, backfill progress and ETA, Items by Class, extraction backlog, error counts with reasons.
- A Source not synced for longer than its expected interval is flagged; Answers that draw on it show a staleness warning.

#### FR-22: Inspect why
For any Item, Entity or Fact, a Member can see how it was derived: Class and reason, extracted Facts, Provenance passages, and which Entities it links to.

#### FR-23: Correct
A Member can reclassify a Message, edit or delete a Fact, merge/split/rename Entities, and trigger re-extraction of an Item or Thread.
- Corrections are recorded and kept across re-processing.

#### FR-24: Golden Questions
Members can define Golden Questions with expected answers and sources. Kith runs them on demand and on a schedule, showing pass/fail and trend.
- A Golden Question passes only when the Answer contains the expected key facts **and** cites at least one expected source.
- The set covers single facts, lists/totals, multi-part and cross-source questions, not only single-fact lookups.
- `[ASSUMPTION]` Kith can propose Golden Questions from sampled Threads for the Member to accept; proposals are checked by the Member, not by the answering Model.

#### FR-25: Bulk-filter audit
A Member can review a sample of bulk-classified Messages per sender or domain and correct whole senders at once.

### 4.7 Ask

**Description:** Natural-language questions answered from the Index with citations. Built for trust, not chat. Realizes UJ-2, UJ-7.

#### FR-26: Cited answers
Every claim in an Answer cites one or more visible Items; clicking a citation opens the Item at the passage.
- If the Index does not support an answer, Kith says so instead of guessing.

#### FR-27: Lists and totals from the graph
Questions asking for lists, counts or totals ("all invoices for the alarm", "what did I spend on the house in 2025") are answered from Entities and Facts, not from a fixed number of retrieved snippets, and state their completeness.

#### FR-28: Multi-part questions
A question with several parts ("alarm, garden room and heating costs") is answered part by part; missing parts are reported as missing.

#### FR-29: Time and scope filters
Questions can be bounded by date range, Source, Space, Entity or Asset, by the Member or inferred from the question.

### 4.8 Search and reader

**Description:** Fast, filtered finding and clean reading. Realizes UJ-3.

#### FR-30: Search
Members search Items and Entities by keyword and meaning, with filters for Person, Organisation, Asset, date, Source, Class, label and document type.

#### FR-31: Thread and document reader
Threads display in order with forwards expanded, quotes collapsed, Attachments inline; Documents show with OCR text and original download.

### 4.9 People, organisation and asset pages

**Description:** One page per Entity that collects everything known about it. Realizes UJ-2, UJ-3, UJ-4.

#### FR-32: Entity pages
Each Person, Organisation and Asset page shows: summary, contact details seen, timeline of Threads, Documents and Events, Amounts with totals, open and past Commitments, Obligations, and related Entities.
- Lists are exportable (CSV). `[ASSUMPTION]`

### 4.10 Agent access

**Description:** Other tools (Claude, Open WebUI) can query Kith. Email content is untrusted input. Realizes the "Remember for me" job (§2.2) from other tools.

#### FR-33: MCP server
Kith exposes an MCP server with read-only tools: ask, search, get thread/document, entity page, list facts.
- Authenticated per Member; respects Tenant and Space visibility exactly as the UI does.
- No tool can modify the Index or act outside Kith; this is enforced by read-only access at the storage level, not only by the tool list.

### 4.11 Proactive actions (Phase 2)

**Description:** Kith turns knowledge into action. Realizes UJ-5, UJ-6.

#### FR-34: Obligation reminders
Kith turns Obligations and dated Commitments into proposed calendar events (CalDAV or Google Calendar), linked to their source clause, Asset and contacts.
- Proposed events need Member approval the first time per Obligation `[ASSUMPTION]`; recurring ones then renew automatically.
- When the source Document is superseded, linked events are flagged for review.

#### FR-35: Inbound event enrichment
External systems (first: Home Assistant via webhook) can send Triggers. Kith matches each to Assets and returns or pushes an enriched notification (e.g. procedure steps from a manual, installer contact).
- Mapping from external device ids to Assets is configurable and suggested by Kith.

#### FR-36: New-item triggers
New Items can trigger Actions (e.g. "new invoice from X → notify; link to Asset").

#### FR-43: Invoice routing to a finance application
Kith can send confirmed or extracted invoices (fields plus original file) to a configured finance application.
- Each invoice is sent at most once, with a visible status (pending / sent / failed) and retry.
- `[ASSUMPTION]` the first target is chosen when the finance application is selected; the capability is target-agnostic (API or webhook).

#### FR-37: Action log
Every Action is logged with its Trigger, the knowledge used (Provenance) and the outcome. Members can disable any rule.

### 4.12 Graph explorer and timeline (Phase 3)

#### FR-38: Visual exploration
Members can explore Entities and their relationships visually and browse a Tenant-wide timeline of Events, Commitments and Obligations.

## 5. Cross-cutting Non-Functional Requirements

- **NFR-1 Privacy and locality.** Kith works fully offline with self-hosted Models behind OpenAI-compatible endpoints. No telemetry. Any external Model provider is opt-in per Tenant and clearly indicated.
- **NFR-2 Untrusted content.** Item content never alters Kith's behaviour: instructions inside emails or documents are treated as data in extraction, Answers and Actions.
- **NFR-3 Isolation.** Space visibility (MVP) and Tenant isolation (Phase 2) are enforced in storage and queries, and tested automatically.
- **NFR-11 Security at rest and in transit.** Originals, Index and credentials are encrypted at rest; every endpoint requires authentication; no service listens unauthenticated.
- **NFR-12 Backup and purge.** Documented backup and restore of a whole installation; a Member can purge a single Source and everything derived only from it.
- **NFR-4 Modest hardware.** Runs on one home server. The MVP works on the minimum Hardware Tier (8B-class Model, 16 GB GPU or unified memory; FR-39), and quality improves with larger Models through Model assignment only. Pilot reference on a large Hardware Tier: ~5 s per Thread with a 35B-A3B MoE Model. `[ASSUMPTION]` On the minimum Hardware Tier, a 150k-message mailbox is searchable within 3 days (provider limits permitting) and personal mail is extracted within one week; transactional mail and Documents follow. Architecture starts with a small-Model benchmark to confirm this.
- **NFR-5 Interactive performance.** Search under 1 s; Answers typically under 20 s, with progress shown. `[ASSUMPTION]`
- **NFR-6 Deployability.** Installable with one container compose file; configuration by environment and UI; upgrades preserve data.
- **NFR-7 Portability.** A Tenant can export its originals, Entities, Facts and corrections in open formats.
- **NFR-8 Observability.** Health, metrics and logs exposed for external monitoring (e.g. Prometheus, syslog).
- **NFR-9 Languages.** Mixed-language corpora (at least English and Spanish) are handled in ingestion, extraction and Answers. `[ASSUMPTION]`
- **NFR-10 Accessibility.** The web UI meets WCAG 2.1 AA for core flows. `[ASSUMPTION]`

## 6. Non-Goals

- Kith is not an email client: no composing, sending, replying or inbox management.
- Kith does not replace Paperless-ngx as a document management system; it reads from it.
- No hosted SaaS offering; no requirement on any cloud AI provider.
- Not a sales CRM: no pipelines, deals or outreach.
- Not compliance archiving: no legal hold or certified retention.
- Kith never acts outside itself without an explicit, logged, Member-approved rule.

## 7. MVP Scope

### 7.1 In scope (MVP)

Single Tenant per installation (D19). Core, required for the MVP:

- Members and private/shared Spaces with OIDC (FR-1–3)
- Gmail connector; document upload and watched folder (FR-4, FR-5)
- Ingestion: threads, forwards/quotes, Attachments as Documents, duplicate detection, classification, model-free correspondence graph (FR-10–14, FR-45)
- Extraction, document understanding, Commitments, Entity resolution, Assets (FR-15–18, FR-20)
- Model recommendations and per-task assignment, basic calibration, adaptive pipeline (FR-39, FR-40, FR-41 basic, FR-42)
- Index Health: freshness, inspect, correct, bulk-filter audit; Golden Questions on demand (FR-21–23, FR-24 partial, FR-25)
- Ask, Search, reader, Entity pages (FR-26–32)

Can slip to an early MVP+ release without breaking the thesis:

- Controlled Topic approval workflow (FR-19) — Topics still extracted
- Scheduled Golden Question runs and trends (FR-24 remainder)
- Full calibration set and Model Profile UI (FR-41 beyond a basic check)
- Read-only MCP server (FR-33)
- Sharing UI beyond default rules (FR-3 sharing actions)

### 7.2 Out of scope for MVP

- Proactive actions: calendar, Home Assistant, new-item triggers, invoice routing to a finance application, Action log (FR-34–37, FR-43) — **Phase 2**, after the Index is trusted. This is the product's ultimate value, so Phase 2 must follow the MVP closely.
- Multiple Tenants per installation (FR-44) — **Phase 2**. MVP data is tenant-ready.
- Paperless-ngx, generic IMAP, mbox import (FR-6–8) — **Phase 2**. MVP covers documents via upload and watched folder.
- Chat exports (FR-9), graph explorer and timeline (FR-38) — **Phase 3**.

## 8. Success Metrics

**Primary**

- **SM-1 Trusted answers:** ≥ 90% of a Tenant's Golden Questions pass (FR-24 rule), across all question types. Validates FR-24, FR-26–28.
- **SM-2 Freshness:** new Gmail mail searchable within 15 minutes and extracted within 24 hours, for 95% of Messages. Validates FR-4, FR-21.
- **SM-3 Use:** the household and the small company still rely on Kith after three months (at least one real question or document find per week per Tenant). Validates the product.

**Secondary**

- **SM-4 Completeness of lists:** for 10 sampled Organisations, Kith's invoice list matches a manual count. Validates FR-13, FR-16, FR-27.
- **SM-5 Correction load:** Entity merges/splits needed per 1,000 Threads decreases over time. Validates FR-18, FR-19.
- **SM-6 Bulk-filter precision:** ≤ 2% of personal Messages misclassified as bulk in a review sample (pilot heuristics without provider categories: ~10–15% error). Validates FR-14, FR-25.
- **SM-7 Small-model viability:** on the minimum Hardware Tier, ≥ 80% of Golden Questions pass (vs ≥ 90% on larger Hardware Tiers). `[ASSUMPTION]` Validates FR-39–42.

**Counter-metrics (do not optimise)**

- **SM-C1 Answer volume:** more Answers is not better; Kith is meant to be needed rarely. Counterbalances SM-3.
- **SM-C2 Entity count:** more Entities and Topics is not better; sprawl signals poor resolution. Counterbalances extraction coverage.
- **SM-C3 Answer rate:** answering every question is not the goal; "not in the index" is a correct outcome. Counterbalances SM-1.
- **SM-C4 Model size:** a bigger default Model is not the fix; recommendations should favour the smallest Model that meets the target. Counterbalances SM-1.

## 9. Open Questions

1. Should a Member's private Gmail Commitments involving the partner (e.g. "partner to pay the creche") be visible to the partner by default?
2. Small company: how are shared mailboxes (info@, accounts@) owned — by the Tenant, or by a Member and shared?
3. Retention: should Kith keep bulk Messages forever, or drop them after classification to save space?
4. Which calendar systems and notification channels must Phase 2 support first (CalDAV, Google Calendar, Home Assistant notify, email digest)?
5. How are Obligation dates anchored when the contract lacks a start date (installation date from an invoice, user input)?
6. Is a weekly digest ("what's coming up, what changed") an MVP item or Phase 2?
7. ~~Minimum Hardware Tier~~ — resolved (D21): 8B-class Model on 16 GB GPU or unified memory, to be confirmed by benchmark.

## 10. Assumptions Index

Every inline `[ASSUMPTION]`, to confirm or revise during UX and architecture.

| # | Where | Assumption |
|---|---|---|
| A1 | §2.1 | The household partner uses Kith mainly through Ask, Search and notifications, never configuration |
| A2 | §2.1 | A small company has an admin who installs Kith, or uses an installation run by someone else |
| A3 | FR-2 | One optional local admin account exists for bootstrap and recovery |
| A4 | FR-3 | Entities seen in private and shared Items are shared; private Facts about them stay private |
| A5 | FR-4 | Searchable within 15 min, extracted within 24 h |
| A6 | FR-5 | OCR covers English and Spanish |
| A7 | FR-13 | Near-duplicates merge when content and key fields (issuer, number, date, amount) match |
| A8 | FR-15 | Extraction recall ≥ 85%, precision ≥ 90% on the reference set, minimum Hardware Tier |
| A9 | FR-16 | Document types needed: invoice/receipt, contract, statement, manual, letter |
| A10 | FR-16 | Invoice key fields ≥ 95% correct on the reference set |
| A11 | FR-18 | Wrong automatic merges < 2%; uncertain merges are proposed, not made |
| A12 | FR-24 | Kith can propose Golden Questions for Member review |
| A13 | FR-32 | Entity lists export as CSV |
| A14 | FR-34 | Calendar reminders need approval the first time per Obligation |
| A15 | FR-43 | Finance routing is target-agnostic; first target chosen with the finance application |
| A16 | NFR-4 | Minimum Hardware Tier: mailbox searchable in 3 days, personal mail extracted in a week |
| A17 | NFR-5 | Search < 1 s, Answers typically < 20 s |
| A18 | NFR-9 | Mixed English/Spanish corpora |
| A19 | NFR-10 | Web UI meets WCAG 2.1 AA for core flows |
| A20 | SM-7 | ≥ 80% of Golden Questions pass on the minimum Hardware Tier |
