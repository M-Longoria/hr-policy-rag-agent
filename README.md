# hr-policy-rag-agent
A fully decoupled, enterprise-grade RAG pipeline and AI agent engineered with n8n, Supabase, Pinecone, and Dify to ingest, split, and semantically query unstructured markdown policies using Google Gemini embeddings.


## 📊 System Evaluation & RAG Triad Matrix
The system was rigorously evaluated across cross-vocabulary, out-of-scope, and direct compliance intent query vectors to measure retrieval accuracy and anti-hallucination defenses:


| Test Case Intent | User Query Sample | Avg Match Score | Context Relevance | Groundedness | System Bolding Rules Met? | Citation Attached? | Pass / Fail |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Direct Policy** | "How do I request jury duty time off?" | `0.704` | 5 / 5 (Excellent) | 5 / 5 (Perfect) | Yes (**Workday**) | Yes | **PASS** |
| **Keyword Sync** | "Can I get caregiver sick leave?" | `0.780` | 5 / 5 (Excellent) | 5 / 5 (Perfect) | Yes (**Tilt**) | Yes | **PASS** |
| **Vocabulary Swap** | "Where do I request vacation?" | `0.650` | 4 / 5 (High) | 5 / 5 (Perfect) | Yes (**Time Off**) | Yes | **PASS** |
| **Out-of-Scope** | "What is the policy for medical leave?" | `0.210` | 0 / 5 (None) | 5 / 5 (Fallback) | N/A (Apology) | No | **PASS** |
| **Vague Prompt** | "How can I take off?" | `0.110` | 0 / 5 (None) | 5 / 5 (Clarify) | N/A (Clarified) | No | **PASS** |


## 🚀 Architectural Overview
This system is a fully decoupled, production-grade Retrieval-Augmented Generation (RAG) pipeline engineered to eliminate LLM hallucinations, maintain strict corporate compliance tone, and optimize semantic search retrieval speeds. By moving away from brittle, all-in-one architectures, this pipeline separates the workflow into highly specialized, isolated infrastructure layers.

### The System Pipeline Stack:
* **Orchestration & Transformation Layer (n8n):** Automates data ingestion, markdown normalization, metadata enrichment, and executes custom REST client API querying.
* **Relational Persistence Layer (Supabase Postgres):** Permanently logs data schemas and mirrors source raw text blocks as a system fail-safe.
* **Vector Intelligence Engine (Pinecone & Google Gemini):** A custom-coded REST network infrastructure that transforms natural language queries into 3072-dimensional vector arrays to locate semantic policy chunks inside dedicated namespaces.
* **Guardrailed Specialist Interface (Dify):** Anchors the conversational engine to rigid compliance guardrails, strict fallback rules, visual emphasis bolding patterns, and automated source citations.

---

## 🛠️ Core Engineering Highlights & Problem Solving
* **Data Ingestion Sanitation:** Implemented an array mapping loop using native JavaScript string `.trim()` rules to intercept and strip trailing line-break anomalies (`\n`) out of file-system arrays, preventing citation parser crashes and metadata corruption.
* **Two-Stage Filtering Funnel Optimization:** Implemented a decoupled retrieval funnel utilizing wide-net database boundaries (**Top-K: 6, Score Threshold: 0.0**) to catch low-distance matches over raw REST HTTP tunnels, delegating the final contextual prioritization to a tight agent layer (**Top-K: 4, Threshold: 0.25**).
* **Dynamic Parameter Mapping:** Resolved cross-node array lookup evaluation blocks on live webhook execution threads by hardcoding production threshold constants and targeting nested JSON body parameters (`$json.body.query`) locally within the incoming stream, ensuring 1-second operational latency and preventing system timeouts.

---

## 🔒 Security & Compliance Governance
* **Credential Rotation Protocol:** Implemented production key separation by executing a full lifecycle rotation across Google AI Studio, Pinecone, and custom n8n Bearer token authentication headers (`Authorization: Bearer [token]`).
* **Strict Source Anchoring:** Prompt configurations strictly limit the agent to the retrieved facts. If a target policy chunk is absent over the API tunnel, the engine completely suppresses hallucination attempts, executing a safe fallback to official handbook URLs or triggering a polite clarification dialogue.
