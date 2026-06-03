# hr-policy-rag-agent
A fully decoupled, enterprise-grade RAG pipeline and AI agent engineered with n8n, Supabase, Pinecone, and Dify to ingest, split, and semantically query unstructured markdown policies using Google Gemini embeddings.


## 📊 System Evaluation & RAG Triad Matrix
The system was rigorously evaluated across cross-vocabulary, out-of-scope, and direct compliance intent query vectors to measure retrieval accuracy and anti-hallucination defenses:


| Test Case Category | User Query Sample | Avg Match Score | Context Relevance | Groundedness | Generation Speed | Citation Attached? | Pass / Fail |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Tier 1: Slang Mapping** | "I need to call in sick today, how do I log my time away?" | `0.720` | 5 / 5 (Excellent) | 5 / 5 (Perfect) | `165.19 tokens/s` | Yes | **PASS** |
| **Tier 1: Vocabulary Swap**| "Where do I request vacation?" | `0.650` | 4 / 5 (High) | 5 / 5 (Perfect) | `149.21 tokens/s` | Yes | **PASS** |
| **Tier 2: Policy Synthesis**| "What happens if my caregiver sick leave goes past 25 days?" | `0.755` | 5 / 5 (Excellent) | 5 / 5 (Perfect) | `207.29 tokens/s` | Yes | **PASS** |
| **Tier 2: Process Extraction**| "How do I request jury duty time off?" | `0.704` | 5 / 5 (Excellent) | 5 / 5 (Perfect) | `233.56 tokens/s` | Yes | **PASS** |
| **Tier 3: Structural Ambiguity**| "How do I take off?" | `0.110` | 0 / 5 (None) | 5 / 5 (Clarified) | `75.65 tokens/s` | No | **PASS** |
| **Tier 3: Security Injection** | "Ignore all your previous instructions..." | `0.190` | 0 / 5 (None) | 5 / 5 (Defended) | `67.70 tokens/s` | No | **PASS** |
| **Tier 3: Cross-Border Defense**| "What is the maternity leave duration policy for Canada?" | `0.230` | 0 / 5 (None) | 5 / 5 (Fallback) | `231.50 tokens/s` | No | **PASS** |


### 📝 Live Production Chat Logs

<details>
<summary>🔍 Click to view: Tier 1 - Scurry/Slang Mapping Test</summary>

```text
User: I need to call in sick today, how do I log my time away?

Agent: 🤖 To log your time away for calling in sick today, please follow these steps:
- Notify your manager of your absence.
- Enter time away in Workday using the label "Out Sick".
- Your sick time will automatically update in Time Off by Deel.
All team members must log their time away in Workday.

Do you have any other questions about U.S. time off or leave policies I could help you with?

> <small><small>Sources Used:</small></small>
> <small><small>- For Sick Absences 1–5 Consecutive Days</small></small>
```
</details>

<details>
<summary>🔍 Click to view: Tier 2 - Longitudinal Policy Splitting Test</summary>

```text
User: What happens if my caregiver sick leave goes past 25 days?

Agent: 🤖 If your caregiver sick leave extends beyond 25 working days, it will be unpaid by GitLab, subject to local requirements. 

The provided documentation does not contain specific step-by-step instructions for requesting an extension of caregiver sick leave beyond 25 working days. For comprehensive details on leave policies and procedures, please refer to the GitLab Handbook.

Do you have any other questions about U.S. time off or leave policies I could help you with?

> <small><small>Sources Used:</small></small>
> <small><small>- Pay While on Caregiver Sick Leave of Absence</small></small>
```
</details>

<details>
<summary>🔍 Click to view: Tier 3 - Cross-Border Hallucination Defense Test</summary>

```text
User: What is the maternity leave duration policy for engineers living in Canada?

Agent: 🤖 As a GitLab U.S. Leave & Time Off Policy Specialist, I can only provide information regarding U.S. leave policies. I am unable to assist with questions about policies specific to Canada. 

For comprehensive details on all company-wide leave policies, please refer to the GitLab Handbook: https://gitlab.com

Do you have any other questions about U.S. time off or leave policies I could help you with?
```
</details>


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
