# Module 3 – Design Options

## Status

Accepted

---

# 1. Purpose

This module evaluates architectural alternatives considered for Audit Workspace and records the rationale behind adopted decisions.

Unlike Module 2, which defines the architecture building blocks, this module explains:

```text
Why a design was selected
Why alternatives were rejected
How future evolution remains possible
```

The module follows the Microsoft Multi-Agent Architecture approach of evaluating multiple patterns before selecting the most appropriate solution.

---

# 2. Design Decision Summary

| Area | Decision |
|--------|----------|
| Multi-Agent Topology | Option A Adopt, Option B Adapt |
| Agent Runtime Placement | Option A Adopt |
| Memory Strategy | Domain-Governed Memory Adopt |
| Retrieval Architecture | Index Per Engagement Adopt |
| Agent Communication | Service Bus Adapt |
| Tool Architecture | Tool Gateway Adopt |
| MCP Usage | Selective MCP Adapt |
| Workflow Pattern | Hybrid Adopt |
| Review Pattern | Risk-Based Review Adopt |
| Search Architecture | Search + Fact Store Adopt |
| Observability | Unified Trace Model Adopt |

---

# 3. Multi-Agent Topology

## Option A – Single Orchestrator + Specialized Agents

Decision: Adopt

Benefits:
- Simpler governance
- Easier traceability
- Easier evaluation
- Easier access control
- Strong auditor explainability

## Option B – Hierarchical Supervisor

Decision: Adapt

Used for complex Audit Q&A workflows requiring decomposition and synthesis.

## Option C – Peer-to-Peer Agent Network

Decision: Reject

Reasons:
- Weak traceability
- Complex governance
- Difficult evaluation
- Difficult audit defensibility

---

# 4. Agent Runtime Placement

## Option A – Local Agents

Decision: Adopt

All agents execute inside the Audit Workspace modular monolith.

Benefits:
- Lower latency
- Easier deployment
- Easier governance
- Easier observability

## Option B – Hybrid Runtime

Decision: Defer

## Option C – Fully Remote Runtime

Decision: Reject

---

# 5. Memory Strategy

## Shared Memory

Decision: Reject

## Agent-Owned Memory

Decision: Reject

## Domain-Governed Memory

Decision: Adopt

Accepted through ADR-032.

---

# 6. Retrieval Architecture

## Shared Index

Decision: Reject

## Index Per Engagement

Decision: Adopt

Accepted through ADR-024.

Benefits:
- Strong isolation
- Easier deletion
- Simpler governance
- Reduced leakage risk

## Hybrid Index Model

Decision: Defer

---

# 7. Agent Communication

## Direct Calls

Decision: Adopt for internal module interactions.

## Azure Service Bus

Decision: Adapt for durable asynchronous workloads.

## Service Bus Everywhere

Decision: Reject.

---

# 8. Tool Architecture

## Direct Tool Access

Decision: Reject.

## Tool Gateway

Decision: Adopt.

## MCP Everywhere

Decision: Reject.

## Selective MCP

Decision: Adapt.

Requires:
- Security Review
- Architecture Review
- Compliance Approval

---

# 9. Workflow Pattern

## Explicit Workflow

Partially Adopted.

## Autonomous Planning

Partially Adopted.

## Hybrid Workflow

Decision: Adopt.

---

# 10. Review Pattern

## Review Everything

Decision: Reject.

## Review Only Final Artifact

Decision: Reject.

## Risk-Based Review

Decision: Adopt.

---

# 11. Search Architecture

## Search Only

Decision: Reject.

## Search + Fact Store

Decision: Adopt.

Accepted through ADR-033.

## Search + Knowledge Graph

Decision: Defer.

---

# 12. Observability Architecture

## Infrastructure Only

Decision: Reject.

## AI Only

Decision: Reject.

## Unified Trace Model

Decision: Adopt.

Accepted through ADR-034.

Trace carries:
- CorrelationId
- CausationId
- TraceId
- AgentVersion
- WorkflowVersion
- EvidenceVersion
- PolicyVersion

---

# 13. Resulting Architecture

```text
Modular Monolith
+ Local Governed Agents
+ Conditional Supervisor
+ Domain-Governed Memory
+ Search + Fact Store
+ Index Per Engagement
+ Tool Gateway
+ Risk-Based Review
+ Unified Trace Model
```

---

# 14. ADRs Introduced

| ADR | Title |
|------|--------|
| ADR-032 | Domain-Governed Memory |
| ADR-033 | Search + Fact Store |
| ADR-034 | Unified Trace Model |
| ADR-035 | Risk-Based Human Review |

---

# 15. Risks

- Supervisor overuse
- Search growth
- Agent sprawl
- Over-review
- MCP misuse

---

# 16. Success Criteria

- Governance remains enforceable.
- Audit traceability remains intact.
- Agents remain manageable.
- Search remains secure.
- Future extraction remains possible.

---

# Module Status

Accepted.
