# Module 6 – Agent Communication

## Status
Accepted

---

# 1. Purpose

This module defines communication patterns for Audit Workspace across:

- Users
- Domain modules
- Agents
- Workflows
- Background workers
- External integrations

The design adopts a hybrid communication model combining:

- In-process commands
- In-process queries
- In-process domain events
- Microsoft Agent Framework workflows
- Server-Sent Events (SSE)
- Azure Service Bus Premium
- Transactional Outbox patterns

---

# 2. Communication Principles

1. Default to in-process communication.
2. Use durable messaging only when necessary.
3. Preserve correlation and causation.
4. Support at-least-once delivery.
5. Require idempotent consumers.
6. Preserve audit traceability.
7. Support future extraction of modules.

---

# 3. Communication Model

```mermaid
flowchart TB

 Client[Audit Canvas]
 API[FastAPI]
 Workflow[Agent Workflow]
 Agents[Specialist Agents]
 Modules[Domain Modules]
 SB[Azure Service Bus]
 Worker[Background Worker]

 Client --> API
 API --> Workflow
 Workflow --> Agents
 Agents --> Modules
 API --> SB
 SB --> Worker
```

---

# 4. Communication Types

| Interaction | Pattern |
|---|---|
| User Request | HTTPS Command/Query |
| Internal State Change | Domain Event |
| Agent Collaboration | Agent Framework Workflow |
| Streaming Response | SSE |
| Durable Processing | Service Bus Queue |
| Multi Consumer Event | Service Bus Topic |

---

# 5. Commands, Queries and Events

## Commands

Examples:

- UploadEvidence
- RegisterEvidence
- SubmitArtifactForReview
- ApproveAuditArtifact

## Queries

Examples:

- RetrieveEvidence
- GetFactVersion
- ResolveCitation

## Events

Examples:

- EvidenceAcceptedForProcessing
- ProposedRiskCreated
- ArtifactApproved

```mermaid
flowchart LR

 Command --> Domain
 Domain --> Event
 Query --> Domain
```

---

# 6. Agent Communication

Agents communicate using typed contracts.

```mermaid
sequenceDiagram

 participant SUP as Supervisor
 participant EXT as Extraction
 participant VAL as Validation
 participant RISK as Risk
 participant QNA as Q&A

 SUP->>EXT: ExecuteSpecialistTask
 EXT-->>SUP: Typed Result

 SUP->>VAL: ExecuteSpecialistTask
 VAL-->>SUP: Typed Result

 SUP->>RISK: ExecuteSpecialistTask
 RISK-->>SUP: Typed Result

 SUP->>QNA: Synthesize
 QNA-->>SUP: Grounded Answer
```

Agents never exchange unrestricted chat transcripts.

---

# 7. Streaming Model

Audit Q&A supports Server-Sent Events.

```mermaid
flowchart LR
 User --> API
 API --> SSE
 SSE --> Client
```

Event Types:

- execution.started
- retrieval.completed
- agent.started
- agent.completed
- citation.available
- answer.delta
- execution.completed
- execution.failed

---

# 8. Azure Service Bus Topology

```mermaid
flowchart TB

 Topic[aw-domain-events]

 Queue1[aw-document-processing]
 Queue2[aw-document-indexing]
 Queue3[aw-agent-background-work]
 Queue4[aw-compliance-export]
 Queue5[aw-external-publication]
```

---

# 9. Transport Matrix

| Interaction | Transport |
|---|---|
| Browser → API | HTTPS |
| Q&A Streaming | SSE |
| Module Calls | In Process |
| Domain Events | In Process |
| Agent Workflow | Microsoft Agent Framework |
| Async Work | Azure Service Bus |

---

# 10. Standard Envelope

```json
{
  "messageId":"uuid",
  "messageType":"EvidenceAcceptedForProcessing",
  "correlationId":"C1",
  "causationId":"M1",
  "schemaVersion":"1.0"
}
```

Required:

- MessageId
- CorrelationId
- CausationId
- Classification
- PolicyVersion

---

# 11. Correlation Model

```mermaid
flowchart LR

 Upload --> Accepted
 Accepted --> Extraction
 Extraction --> Searchable
```

Each message preserves:

- CorrelationId
- TraceId
- CausationId

---

# 12. Transactional Outbox

## SQL Pattern

```mermaid
sequenceDiagram

 Module->>SQL: Save State
 Module->>SQL: Save Outbox
 SQL-->>Module: Commit
 Dispatcher->>Bus: Publish
```

## Cosmos Pattern

```mermaid
sequenceDiagram

 Module->>Cosmos: Aggregate + Outbox Batch
 Cosmos-->>Module: Commit
 ChangeFeed->>Bus: Publish
```

---

# 13. Delivery Semantics

Adopted:

```text
At-Least-Once Delivery
+
Idempotent Consumers
```

Exactly-once is not claimed.

---

# 14. Idempotency

Consumer Inbox Pattern:

```mermaid
flowchart LR
 Message --> Inbox
 Inbox --> Check
 Check --> Process
```

Examples:

- Evidence registration key
- Extraction key
- Indexing key
- Review decision key

---

# 15. Retry Policy

| Failure | Action |
|---|---|
| Network Error | Retry |
| Throttling | Retry |
| Authorization Failed | No Retry |
| Schema Failure | Dead Letter |
| Safety Rejection | Governed Failure |

---

# 16. Dead Letter Strategy

```mermaid
flowchart LR
 Message --> Consumer
 Consumer --> DLQ
 DLQ --> Investigation
 Investigation --> Replay
```

Replay requires authorization.

---

# 17. Message Catalog

Core Messages:

- MM-001 UploadEvidence
- MM-002 EvidenceAcceptedForProcessing
- MM-003 DocumentExtractionCompleted
- MM-004 IndexEvidence
- MM-005 EvidenceSearchable
- MM-006 AnswerAuditQuestion
- MM-007 ExecuteSpecialistTask
- MM-008 RunValidationRuleSet
- MM-009 ProposedRiskCreated
- MM-010 SubmitArtifactForReview
- MM-011 ApproveAuditArtifact
- MM-012 ArtifactApproved
- MM-013 AgentVersionActivated

---

# 18. Security Controls

- Managed Identity
- Classification-aware messaging
- No secrets in messages
- No evidence payloads in durable messages
- Tool reauthorization
- Audit logging

---

# 19. Evolution Path

```mermaid
flowchart LR
 InProcess --> Adapter
 Adapter --> APIorBus
 APIorBus --> ExtractedModule
```

Logical contracts remain unchanged.

---

# 20. ADRs

| ADR | Decision |
|---|---|
| ADR-058 | Hybrid Communication Model |
| ADR-059 | Azure Service Bus Premium |
| ADR-060 | At-Least-Once Delivery |
| ADR-061 | Transactional Outbox |
| ADR-062 | SSE Streaming |
| ADR-063 | Typed Agent Contracts |
| ADR-064 | No Evidence In Durable Messages |
| ADR-065 | Aggregate Scoped Ordering |

---

# 21. Risks

| Risk | Mitigation |
|---|---|
| Duplicate Delivery | Idempotency |
| Lost Event | Outbox |
| Queue Backlog | Scale Workers |
| Confidential Data Leakage | Reference-only Messages |
| Agent Timeout | Step Checkpointing |

---

# 22. Acceptance Checklist

- [x] Hybrid communication model
- [x] Service Bus topology
- [x] Commands queries events
- [x] Agent contracts
- [x] SSE streaming
- [x] Correlation model
- [x] Outbox pattern
- [x] Retry model
- [x] Dead letter handling
- [x] Message catalog
- [x] ADR-058 through ADR-065

---

# Module Status

Accepted.
