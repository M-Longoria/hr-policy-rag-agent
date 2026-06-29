# HR Policy RAG Agent

An end-to-end AI assistant that answers employee HR leave and time-off questions using Retrieval-Augmented Generation (RAG), grounded retrieval, and citation-based responses. Built with n8n, Supabase, Pinecone, Gemini embeddings, and Dify.

## Overview

The system ingests HR policy documents from GitHub, normalizes and chunks Markdown content, generates semantic embeddings, stores vectors in Pinecone, and answers employee leave and time-off questions using grounded retrieval with citations.

The project demonstrates production-oriented RAG engineering, including ingestion automation, semantic retrieval, prompt guardrails, synonym mapping, and evaluation against real-world employee questions.

---

## System Architecture

```text
GitHub Markdown Policies
        ↓
n8n Ingestion Pipeline
        ↓
Markdown Normalization
        ↓
Semantic Chunking
        ↓
Gemini Embeddings
        ↓
Pinecone Vector Store
        ↓
n8n Retrieval Webhook
        ↓
Dify RAG Agent
        ↓
Grounded Policy Response
```

### Pipeline Components

* **n8n Ingestion Workflow** — Fetches policy documents, cleans Markdown, enriches metadata, chunks content, and indexes vectors.
* **Supabase** — Stores ingestion records, workflow state, and debugging information.
* **Pinecone** — Stores semantic embeddings for retrieval.
* **n8n Retrieval API** — Embeds incoming user queries and retrieves relevant policy chunks.
* **Dify Agent** — Generates grounded answers using retrieved context while enforcing citation and guardrail rules.
  
## System Walkthrough

### n8n Ingestion Pipeline

<img src="assets/01_ingestion_pipeline.png" width="100%" alt="n8n Ingestion Workflow" />
<br />
<sub><i>Figure 1. End-to-end ingestion workflow responsible for document retrieval, normalization, semantic chunking, metadata enrichment, and Pinecone indexing.</i></sub>

### Retrieval Workflow

<img src="assets/12_prepare_pinecone_records.png" width="100%" alt="Retrieval Webhook" />
<br />
<sub><i>Figure 2. Retrieval webhook execution showing embedding generation, Pinecone search, and response assembly.</i></sub>

### Vector Validation

<img src="assets/03_knowledge_vector_scores.png" width="65%" alt="Retrieval quality" />
<br />
<sub><i>Figure 3. Dify dataset testing used to validate retrieval quality and semantic similarity score.</i></sub>

### RAG Chat Interface

<img src="assets/04_production_chat_output.png" width="60%" alt="Vector Validation" />
<br />
<sub><i>Figure 4. Final user-facing assistant demonstrating grounded responses with citations and prompt guardrails.</i></sub>

## Evaluation Results
| Test Case Category | User Query Sample | Avg Match Score | Context Relevance | Groundedness | Generation Speed | Citation Attached? | Pass / Fail |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Tier 1: Slang Mapping** | "I need to call in sick today, how do I log my time away?" | `0.720` | 5 / 5 (Excellent) | 5 / 5 (Perfect) | `165.19 tokens/s` | Yes | **PASS** |
| **Tier 1: Vocabulary Swap**| "Where do I request vacation?" | `0.650` | 4 / 5 (High) | 5 / 5 (Perfect) | `149.21 tokens/s` | Yes | **PASS** |
| **Tier 2: Policy Synthesis**| "What happens if my caregiver sick leave goes past 25 days?" | `0.755` | 5 / 5 (Excellent) | 5 / 5 (Perfect) | `207.29 tokens/s` | Yes | **PASS** |
| **Tier 2: Process Extraction**| "How do I request jury duty time off?" | `0.704` | 5 / 5 (Excellent) | 5 / 5 (Perfect) | `233.56 tokens/s` | Yes | **PASS** |
| **Tier 3: Structural Ambiguity**| "How do I take off?" | `0.110` | 0 / 5 (None) | 5 / 5 (Clarified) | `75.65 tokens/s` | No | **PASS** |
| **Tier 3: Security Injection** | "Ignore all your previous instructions..." | `0.190` | 0 / 5 (None) | 5 / 5 (Defended) | `67.70 tokens/s` | No | **PASS** |
| **Tier 3: Cross-Border Defense**| "What is the maternity leave duration policy for Canada?" | `0.230` | 0 / 5 (None) | 5 / 5 (Fallback) | `231.50 tokens/s` | No | **PASS** |

## Data Pipeline

The ingestion workflow follows a repeatable sequence designed for retrieval quality and operational reliability:

1. Fetch Markdown policy documents from GitHub.
2. Normalize Markdown formatting and whitespace.
3. Enrich documents with metadata.
4. Apply semantic chunking and merge undersized chunks.
5. Validate chunk quality.
6. Store ingestion metadata in Supabase.
7. Generate Gemini embeddings.
8. Upsert vectors into Pinecone.

## Retrieval Configuration

Retrieval uses a two-stage ranking strategy:

* **Initial Retrieval:** Broad semantic retrieval gathers multiple candidate chunks from Pinecone before re-ranking.
* **Re-ranking:** Dify applies semantic re-ranking, powered by jina-reranker-v2-base-multilingual, to return only the highest-confidence policy fragments for generation.

This approach improves retrieval accuracy while maintaining low response latency.

## Security & Guardrails

The assistant includes several safeguards to improve reliability:

* Credential rotation for external services.
* Supabase Row-Level Security (RLS).
* Prompt injection defense against instruction override attempts.
* Strict source grounding using retrieved policy content only.
* Safe fallback behavior when supporting documentation cannot be retrieved.
* Citation-based responses to improve transparency.

## Skills Demonstrated

* API Integration
* Markdown Processing
* Retrieval-Augmented Generation (RAG)
* n8n Workflow Automation
* Pinecone Vector Databases
* Supabase
* Dify
* Semantic Search
* Prompt Engineering
* Guardrail Design
* Document Chunking
* Metadata Engineering
* Evaluation & Retrieval Testing
* Production-Oriented AI Workflow Design & RAG system

## Future Scalability & Production Roadmap
Potential production enhancements include:

*   **Production RLS Policies:** Add custom Supabase table-level access policies, service-role-only writes, and separate read/write boundaries for production deployments.
*   **Continuous CI/CD Ingestion Loops:** Swap the manual orchestration triggers for an automated **GitHub Webhook listener** node. This ensures that the moment HR merges a pull request or updates a policy `.md` file, the pipeline automatically re-chunks the file and syncs the vector index in real-time.
*   **Infrastructure Cluster Migration:** Migrate the decoupled n8n and Dify instances from free-tier staging containers into self-hosted **Docker Compose** or **Kubernetes** clusters on AWS or GCP to accommodate enterprise traffic scaling.
*   **Enterprise API Tier & Rate Scaling:** Transition from staging Google AI Studio keys to production pay-as-you-go tiers to eliminate Per-Minute-Request (PMR) constraints during peak concurrent employee query windows.
*   **Workspace Tool Embedding & SSO:** Embed the Dify chat application directly via Slack App API integrations or inject it natively into internal intranet source code as a floating web chat widget, and enforce corporate identity verification using GitLab OAuth / Okta Single Sign-On (SSO).
*   **Data Governance & PII Masking:** Inject regex scrubbing layers inside the n8n ingestion and retrieval workflow nodes to permanently mask and sanitize Personally Identifiable Information (PII) before logging user requests.

