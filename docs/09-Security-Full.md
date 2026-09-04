# Module 9 – Security

## Status
Accepted

---

# 1. Purpose

Security protects sensitive audit evidence, findings, facts, workflows, reviews, and AI interactions.

Security objectives:

- Confidentiality
- Integrity
- Availability
- Traceability
- Non-repudiation
- Regulatory compliance

The architecture adopts:

```text
Zero Trust
+
Least Privilege
+
Defense in Depth
```

---

# 2. Security Architecture

```mermaid
flowchart TB
    User[Auditor]
    Entra[Microsoft Entra ID]
    API[FastAPI]
    AuthZ[Authorization Layer]
    Agent[Agent Framework]
    Gateway[Tool Gateway]
    Data[Data Stores]
    Audit[Audit Trail]

    User --> Entra
    Entra --> API
    API --> AuthZ
    AuthZ --> Agent
    Agent --> Gateway
    Gateway --> Data
    AuthZ --> Audit
```

---

# 3. Security Principles

1. Never trust, always verify.
2. Engagement is the primary security boundary.
3. Agents cannot expand privileges.
4. Every action is auditable.
5. Evidence remains authoritative.

---

# 4. Identity Architecture

```mermaid
flowchart LR
 User --> MFA
 MFA --> EntraID
 EntraID --> AccessToken
 AccessToken --> API
```

Authentication:

- Single Sign-On
- Multi-Factor Authentication
- Conditional Access
- Federated Identity

---

# 5. Authorization Architecture

```mermaid
flowchart TB
 User --> RBAC
 RBAC --> ABAC
 ABAC --> Resource
 Resource --> Decision
```

RBAC:

- Auditor
- Audit Manager
- Compliance Reviewer
- System Administrator
- AI Platform Administrator

ABAC:

- Engagement
- Classification
- Workflow State
- Ownership

---

# 6. Engagement Security Boundary

```mermaid
flowchart LR
 UserA --> ENG1
 UserB --> ENG2
 ENG1 -. denied .-> ENG2
```

Primary authorization boundary:

```text
Engagement
```

ADR-079

---

# 7. Agent Security

```mermaid
flowchart LR
 UserContext --> Agent
 Policy --> Agent
 ToolGrant --> Agent
 Agent --> Execution
```

Agents receive:

- User Context
- Policy Context
- Tool Grants

Agents cannot:

- Elevate privileges
- Approve conclusions
- Bypass governance

ADR-080

---

# 8. Tool Security

```mermaid
flowchart TB
 Agent --> Gateway
 Gateway --> Auth
 Auth --> Tool
 Tool --> Result
```

ADR-081

Tools cannot be directly invoked.

---

# 9. Prompt Injection Protection

```mermaid
flowchart LR
 RetrievedContent --> Validation
 Validation --> ContextBuilder
 ContextBuilder --> Agent
```

Controls:

- Retrieved content treated as data
- Policies externalized
- Reauthorization required
- Context isolation

ADR-082

---

# 10. Retrieval Security

```mermaid
flowchart LR
 Auth --> Search
 Search --> Filters
 Filters --> Results
```

Filters:

- Engagement
- Classification
- Authorization

No bypass permitted.

---

# 11. Data Classification

| Classification | Examples |
|---|---|
| Restricted Audit | Conclusions, approvals |
| Confidential Audit | Evidence, facts |
| Internal | Telemetry |

---

# 12. Encryption Architecture

```mermaid
flowchart LR
 Data --> EncryptAtRest
 Client --> TLS
 TLS --> Services
```

At Rest:

- Blob Storage
- Cosmos DB
- Search
- Service Bus

In Transit:

- TLS
- HTTPS

---

# 13. Secrets Management

```mermaid
flowchart LR
 App --> ManagedIdentity
 ManagedIdentity --> KeyVault
 KeyVault --> Secret
```

Rules:

- No secrets in prompts
- No secrets in registry
- No secrets in source code

ADR-083

---

# 14. Network Security

```mermaid
flowchart TB
 Client --> WAF
 WAF --> ContainerApps
 ContainerApps --> PrivateEndpoints
 PrivateEndpoints --> Blob
 PrivateEndpoints --> Cosmos
 PrivateEndpoints --> Search
```

Controls:

- Private endpoints
- Network isolation
- Restricted egress
- WAF

ADR-084

---

# 15. Managed Identity Security

Runtime identities:

- API
- Worker
- Indexer
- Dispatcher

Shared secrets prohibited.

---

# 16. Registry Security

```mermaid
flowchart LR
 Admin --> Registry
 Runtime --> ReadOnly
 Registry --> Audit
```

Activation requires:

- Approval
- Evaluation pass

---

# 17. Workflow Security

Workflow resumption validates:

- Agent Version
- Workflow Version
- Authorization
- Engagement

Expired authorization stops execution.

---

# 18. Review Security

```mermaid
flowchart LR
 Draft --> Reviewer
 Reviewer --> Approval
 Reviewer --> Rejection
```

Controls:

- Separation of Duties
- No self approval
- Immutable decisions

---

# 19. Audit Security

```mermaid
flowchart TB
 Access --> Audit
 Approval --> Audit
 Override --> Audit
 Policy --> Audit
```

Audit retention:

```text
7 Years
```

ADR-085

---

# 20. Security Monitoring

```mermaid
flowchart LR
 Event --> Detection
 Detection --> Alert
 Alert --> Investigation
```

Monitor:

- Failed logins
- Prompt injections
- Privilege escalation
- Data-access violations

---

# 21. External MCP Security

```mermaid
flowchart LR
 MCP --> Review
 Review --> Security
 Security --> Approval
```

Requirements:

- Security review
- Architecture review
- Compliance approval

---

# 22. Incident Response

```mermaid
flowchart LR
 Detect --> Contain
 Contain --> Investigate
 Investigate --> Recover
 Recover --> Review
```

Severity:

- Critical
- High
- Medium
- Low

---

# 23. ADRs

| ADR | Decision |
|---|---|
| ADR-078 | Zero Trust Security Model |
| ADR-079 | Engagement Security Boundary |
| ADR-080 | Agents Cannot Expand Privileges |
| ADR-081 | Tool Calls Require Reauthorization |
| ADR-082 | Prompt Context Is Not Security Boundary |
| ADR-083 | Managed Identity by Default |
| ADR-084 | Private Connectivity for Production |
| ADR-085 | Separate Audit Records from Telemetry |

---

# 24. Risks

| Risk | Mitigation |
|---|---|
| Prompt Injection | Context isolation |
| Data Leakage | Authorization |
| Privilege Escalation | Reauthorization |
| Secret Exposure | Key Vault |
| Registry Tampering | Governance approval |

---

# 25. Acceptance Checklist

- [x] Identity architecture
- [x] Authorization architecture
- [x] Engagement security boundary
- [x] Agent security
- [x] Tool security
- [x] Retrieval security
- [x] Encryption architecture
- [x] Secrets management
- [x] Network security
- [x] Workflow security
- [x] Review security
- [x] Audit security
- [x] Monitoring
- [x] Incident response
- [x] ADR-078 through ADR-085

---

# Module Status

Accepted.
