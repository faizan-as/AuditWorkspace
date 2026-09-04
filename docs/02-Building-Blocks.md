# Module 2 – Building Blocks

## Status
Accepted

---

# 1. Purpose

This module defines the primary architectural building blocks that implement the Audit Workspace solution. It converts business capabilities into deployable components, runtime services, storage systems, communication mechanisms, and governance-aware AI capabilities.

The architecture follows:

- Modular Monolith First
- Azure Container Apps deployment
- Governed multi-agent architecture
- Retrieval-Augmented Generation (RAG)
- Human-in-the-loop decision making
- Evidence traceability
- Audit defensibility

---

# 2. High-Level Building Block Architecture

```mermaid
flowchart TB

    User[Auditor / Audit Manager]

    Web[Audit Canvas\nReact + Next.js]

    API[Audit Workspace API\nFastAPI]

    Worker[Audit Workspace Worker\nFastAPI Runtime]

    Blob[Azure Blob Storage]
    Cosmos[Azure Cosmos DB]
    Search[Azure AI Search]
    DocIntel[Azure AI Document Intelligence]
    Foundry[Microsoft Foundry]
    ServiceBus[Azure Service Bus Premium]

    User --> Web
    Web --> API

    API --> Blob
    API --> Cosmos
    API --> Search
    API --> Foundry

    API --> ServiceBus

    ServiceBus --> Worker

    Worker --> Blob
    Worker --> Search
    Worker --> Cosmos
    Worker --> DocIntel
```

---

# 3. Deployable Units

## Audit Workspace Web

Technology:

- React
- Next.js

Responsibilities:

- Audit Canvas
- Evidence viewing
- AI chat experience
- Review workflows
- Risk visualization
- Approval workflows

---

## Audit Workspace API

Technology:

- Python FastAPI
- Microsoft Agent Framework

Responsibilities:

- Authentication
- Authorization
- Agent orchestration
- Retrieval orchestration
- Governance enforcement
- Review APIs
- Search APIs

---

## Audit Workspace Worker

Same codebase as API.

Responsibilities:

- OCR processing
- Document extraction
- Chunking
- Embedding generation
- Search indexing
- Long-running workflows

---

# 4. Modular Monolith Structure

```mermaid
flowchart LR

    Identity[Identity & Access]
    Engagement[Engagement Workspace]
    Evidence[Evidence Catalog]
    Analysis[Audit Analysis]
    Validation[Validation Rules]
    Review[Review & Approval]

    Processing[Document Processing]
    Retrieval[Retrieval & Indexing]
    Registry[Agent Management]
    Orchestration[Agent Orchestration]
    Audit[Audit Trail]
    Governance[Administration & Governance]
```

## Core Modules

### Identity & Access

Owns:

- Role assignments
- User authorization
- Access policies

### Engagement Workspace

Owns:

- Engagement metadata
- Workspace lifecycle

### Evidence Catalog

Owns:

- Evidence metadata
- Evidence versions
- Classification
- Provenance

### Audit Analysis

Owns:

- Facts
- Risks
- Findings
- Summaries

### Review & Approval

Owns:

- Reviews
- Approvals
- Decisions

---

# 5. Agent Building Blocks

## Agent Inventory

| Agent | Responsibility |
|---------|---------|
| Audit Q&A Agent | Grounded Q&A |
| Evidence Extraction Agent | Extract facts |
| Risk Detection Agent | Detect risks |
| Validation Agent | Validation support |
| Summary Agent | Summaries |
| Workflow Assistant Agent | Workflow guidance |
| Audit Q&A Supervisor | Complex orchestration |

---

## Agent Architecture

```mermaid
flowchart TB

    Supervisor[Audit Q&A Supervisor]

    QnA[Audit Q&A Agent]
    Extract[Evidence Extraction Agent]
    Risk[Risk Detection Agent]
    Validate[Validation Agent]
    Summary[Summary Agent]

    Supervisor --> Extract
    Supervisor --> Risk
    Supervisor --> Validate
    Supervisor --> Summary

    Extract --> QnA
    Risk --> QnA
    Validate --> QnA
    Summary --> QnA
```

---

# 6. Data Architecture

## Authoritative Data Stores

### Azure Blob Storage

Stores:

- Financial statements
- Trial balances
- Ledgers
- Invoices
- Receipts
- Audit evidence

Role:

**Authoritative evidence repository**.

---

### Azure Cosmos DB

Stores:

- Facts
- Workflow memory
- Conversation memory
- Agent execution memory
- Agent registry

Role:

Application operational state.

---

## Derived Data Stores

### Azure AI Search

Stores:

- Chunks
- Embeddings
- Search documents

Role:

Derived and rebuildable retrieval layer.

---

# 7. Document Processing Pipeline

```mermaid
flowchart LR

    Upload[Document Upload]
        --> Store[Blob Storage]
        --> OCR[Document Intelligence]
        --> Extract[Extraction]
        --> Normalize[Normalization]
        --> Chunk[Chunking]
        --> Embed[Embeddings]
        --> Index[Indexing]
        --> Searchable[Search Ready]
```

---

# 8. Retrieval Architecture

Decision:

**Index Per Engagement**

```mermaid
flowchart TB

    Engagement1[Index ENG-001]
    Engagement2[Index ENG-002]
    Engagement3[Index ENG-003]

    Search[Azure AI Search]

    Search --> Engagement1
    Search --> Engagement2
    Search --> Engagement3
```

Benefits:

- Engagement isolation
- Easier retention
- Easier governance
- Reduced leakage risk

---

# 9. Runtime Flows

## Audit Question Flow

```mermaid
sequenceDiagram

    participant User
    participant API
    participant Search
    participant Agent

    User->>API: Ask Question
    API->>Search: Retrieve Evidence
    Search-->>API: Relevant Chunks
    API->>Agent: Grounded Request
    Agent-->>API: Cited Response
    API-->>User: Response
```

---

## Complex Audit Q&A

```mermaid
sequenceDiagram

    participant User
    participant Supervisor
    participant Risk
    participant Validation
    participant Summary

    User->>Supervisor: Complex Question

    Supervisor->>Risk: Analyze Risks
    Supervisor->>Validation: Run Validation
    Supervisor->>Summary: Summarize

    Risk-->>Supervisor: Result
    Validation-->>Supervisor: Result
    Summary-->>Supervisor: Result
```

---

# 10. Messaging Architecture

## Azure Service Bus Topology

```mermaid
flowchart TB

    APIOUT[API]

    Queue1[document-processing]
    Queue2[document-indexing]
    Queue3[agent-background-work]
    Queue4[compliance-export]

    Topic[domain-events]

    Worker[Workers]

    APIOUT --> Queue1
    APIOUT --> Queue2
    APIOUT --> Topic

    Queue1 --> Worker
    Queue2 --> Worker
```

---

# 11. Security Building Blocks

- Microsoft Entra ID
- RBAC + ABAC
- Managed Identity
- Azure Key Vault
- Private Endpoints
- Customer Managed Keys
- Audit Trail
- Tool Gateway Authorization

---

# 12. Observability Building Blocks

Trace Context:

- CorrelationId
- CausationId
- TraceId
- EngagementId
- AgentVersion
- WorkflowVersion

Monitoring:

- API Latency
- Search Latency
- Agent Executions
- Cost
- Token Usage
- Queue Depth

---

# 13. Human Review Gates

Mandatory approval required for:

- Findings
- Conclusions
- Publication
- Retention Exceptions
- Governance Exceptions

No self-approval permitted.

---

# 14. Key Architecture Decisions

- Azure Container Apps
- Azure Blob Storage authority
- Azure Cosmos DB operational state
- Azure Service Bus Premium
- Index-per-engagement
- Local governed agents
- Conditional supervisor
- Human review required

---

# 15. Risks

| Risk | Mitigation |
|--------|---------|
| Agent sprawl | Registry governance |
| Search drift | Rebuildable indexes |
| Retrieval degradation | Evaluation framework |
| Prompt injection | Tool gateway and policy controls |
| Queue backlog | Autoscaling and backpressure |

---

# 16. Acceptance Checklist

- [x] Deployables accepted
- [x] Module decomposition accepted
- [x] Agent architecture accepted
- [x] Data architecture accepted
- [x] Search architecture accepted
- [x] Messaging architecture accepted
- [x] Security building blocks accepted
- [x] Review gates accepted

---

# Module Status

Accepted.
