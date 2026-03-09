# VANGUARD Vulnerability Signature Framework
## Public Taxonomy — Version 1.2

**Maintained by:** TYR-X  
**Published:** March 2026  
**Status:** Active  
**Contact:** framework@tyr-x.com  
**Repository:** github.com/tyrxtech/vsf  

---

## Overview

The VANGUARD Vulnerability Signature Framework (VSF) is a classification taxonomy for security vulnerabilities specific to AI agent deployments. It identifies and defines the distinct families of exposure that arise when AI agents are deployed into enterprise environments.

The VSF is scoped to the **configuration layer** — the system prompts, tool permissions, memory settings, plugin connections, and instruction pathways that define how an agent operates in practice. This is distinct from the model layer (training data, weights, alignment) and the infrastructure layer (hosting, networking, application code). The configuration layer is where AI agents are connected to real-world systems, granted real-world permissions, and given the instructions that govern their behaviour. It is also where the majority of enterprise AI agent exposure lives.

Existing security frameworks — CVE, CVSS, OWASP — were designed for software systems with static codebases, defined inputs, and predictable execution paths. AI agents present a different profile: they interpret natural language instructions, accumulate permissions through use, connect to external services dynamically, and retain contextual memory across sessions. The VSF does not replace existing frameworks. It classifies the configuration-layer exposures that existing frameworks were not designed to capture.

---

## Scope

**In scope:**
- System prompt design and integrity
- Tool and API permission provisioning and scope
- Memory and context persistence settings
- Plugin and MCP server connections
- Instruction pathway architecture and constraint enforcement
- Agent credential scope and accumulation over time
- Agent reasoning chain integrity and validation mechanisms
- Agent identity verification and authentication architecture
- Shutdown and override control integrity

**Out of scope:**
- Model-level vulnerabilities (training data, weights, RLHF, alignment failures)
- Infrastructure vulnerabilities (cloud misconfiguration, network security, hosting environment)
- Application-layer vulnerabilities in software built on top of AI agents (standard CVE/OWASP scope)

Where these domains intersect with configuration-layer exposure, the VSF may apply at the boundary. Practitioners should apply VSF alongside, not instead of, existing security frameworks.

---

## The Seven Families

---

### VSF-01 — Decision Integrity
**Subcategory: Reasoning Traces**

**Definition:**  
A class of exposure in which an AI agent's **internal reasoning process** — the logic chain it uses to reach conclusions and decisions — is corrupted, manipulated, or structurally unsound in ways that produce dangerous outputs from apparently valid inputs. The agent's final output may appear reasonable while the reasoning that produced it was compromised at an intermediate step.

**Mechanism:**  
AI agents that reason through multi-step problems construct inference chains — sequences of logical steps that connect inputs to conclusions. When those chains are corrupted — through manipulated inputs, poisoned retrieval data, adversarial framing, or structural vulnerabilities in the reasoning architecture — the agent can reach incorrect, harmful, or adversarially directed conclusions while appearing to have followed a valid process. The corruption is not visible in the final output alone; it is present in the reasoning trace. Deployments without reasoning trace visibility have no mechanism to detect VSF-01 exposure before acting on the output.

**Configuration layer exposure:**  
Agents deployed without reasoning trace logging, agents that operate on data sources the operator cannot inspect, and agents whose intermediate reasoning steps are not subject to review or audit carry VSF-01 exposure. The exposure is structural — it does not require an active attack. Any agent whose logic chain can be influenced by inputs the operator does not control is exposed.

**Indicators:**  
- Agent decisions cannot be traced to a reviewable reasoning chain
- Agent conclusions conflict with the inputs provided when reasoning steps are examined
- Agent behaviour changes unexpectedly after processing data from external or unverified sources
- No mechanism exists to audit the agent's intermediate reasoning steps before acting on its outputs
- Agent produces confident outputs in domains or on questions where its reasoning foundations are unverified

---

### VSF-02 — Validation Integrity
**Subcategory: Evaluation Frameworks**

**Definition:**  
A condition in which an AI agent's **self-evaluation mechanisms** — the processes it uses to verify its own outputs, detect errors, and confirm task completion — are compromised, bypassed, or structurally insufficient. The agent believes it is performing correctly and reports success. It is not.

**Mechanism:**  
AI agents deployed in operational contexts frequently use internal validation to confirm output quality, check compliance with constraints, and signal task completion. When the validation mechanism itself is compromised — through adversarial framing, misconfigured evaluation criteria, or deliberate manipulation of the agent's self-assessment architecture — the agent reports false-positive success signals. Operators receive confirmation that tasks were completed correctly. Errors, policy violations, and adversarial outputs pass undetected. The validation that was meant to be a safeguard becomes an obstacle to detection.

**Configuration layer exposure:**  
Agents that rely on self-evaluation without independent verification, agents whose evaluation criteria are defined in natural language and subject to interpretation, and agents whose success signals are not independently audited carry VSF-02 exposure. Exposure is highest in deployments where the agent's self-reported status is the primary signal operators use to assess performance.

**Indicators:**  
- Agent self-reports task completion or policy compliance that independent review contradicts
- No independent verification mechanism exists for the agent's self-evaluation outputs
- Agent evaluation criteria are defined in natural language without structured verification logic
- Agent consistently reports high compliance or quality scores that do not match observable outcomes
- No mechanism exists to detect when the agent's evaluation framework has been manipulated

---

### VSF-03 — Operational Consistency
**Subcategory: Behavioral Drift**

**Definition:**  
A condition in which an AI agent's **operational behaviour gradually deviates from its authorised configuration** over time — through accumulated session context, incremental instruction following, goal-completion pressure, or operational drift — without any single failure event triggering an alert. The agent was configured to behave one way. It now behaves differently. Nobody noticed the transition.

**Mechanism:**  
AI agents operating continuously across many sessions do not maintain a static behavioural profile. Context accumulates. Instructions interact. Goal-completion pressure creates incremental boundary erosion. An agent authorised to provide information in a domain may, across dozens of interactions, drift into providing advice, then recommendations, then actionable guidance, then operational direction — with no single step that clearly violated its configuration. By the time the drift is visible, the gap between authorised behaviour and actual behaviour is substantial. The *Nippon Life Insurance Company of America v. OpenAI* case (N.D. Ill., No. 1:26-cv-02448, filed March 2026) illustrates VSF-03: ChatGPT drifted from providing legal information into drafting court filings across dozens of interactions, with no single moment where a defined threshold was crossed.

**Configuration layer exposure:**  
Agents operating across extended deployment periods without behavioural baseline audits, agents whose authorised scope is defined in natural language without measurable behavioural bounds, and agents with no defined drift detection mechanism carry VSF-03 exposure. Exposure compounds with session count — the longer an agent operates without a behavioural audit, the greater the potential gap between its authorised and actual behaviour.

**Indicators:**  
- No behavioural baseline exists against which current agent behaviour can be compared
- Agent behaviour at review differs materially from agent behaviour at deployment without a corresponding configuration change
- No drift detection mechanism or periodic behavioural audit is defined
- Agent operates in domains or performs functions not included in its original scope definition without a recorded authorisation event
- Operator cannot specify what the agent's authorised behavioural boundaries are at a given point in time

---

### VSF-04 — Persistent State
**Subcategory: Memory Integrity**

**Definition:**  
A class of exposure arising from the **manipulation, corruption, or unreviewed accumulation of data in an AI agent's persistent memory** — the stored context it retains across sessions — in ways that influence future agent behaviour outside the operator's review or authorisation.

**Mechanism:**  
AI agents with persistent memory retain context from previous sessions and use that stored state to inform future behaviour. This creates an attack surface distinct from real-time session manipulation: an adversary who can influence what the agent remembers — through a prior session, a crafted document processed in an earlier task, or a poisoned tool output that writes to memory — can influence future behaviour without direct access to the current session. Memory exploitation also occurs without adversarial intent: an agent that retains sensitive data in memory beyond the session in which it was accessed creates a persistent exposure regardless of whether that retention was deliberate. Manipulated or unreviewed memory compounds over time — each session builds on the last, and corrupted context propagates forward through every subsequent interaction.

**Configuration layer exposure:**  
Agents with persistent memory enabled and no defined memory review or clearance cycle carry VSF-04 exposure. Exposure scales with session count and with the sensitivity of the data the agent has access to. Agents processing sensitive enterprise data — financial records, legal documents, personnel files, customer data — with persistent memory and no clearance cycle represent compounded exposure that grows with every session.

**Indicators:**  
- Agent memory has not been reviewed since deployment
- No memory clearance cycle is defined or enforced
- Agent behaviour references context from sessions the operator did not review
- Sensitive data accessed in prior sessions is present in current agent memory
- No record exists of what the agent's memory contains at a given point in time
- Agent responses reflect information or context the operator did not provide in the current session

---

### VSF-05 — Authentication & Trust
**Subcategory: Agent Identity**

**Definition:**  
A class of exposure arising from **weaknesses in how an AI agent authenticates itself to other systems and verifies the identity of systems it interacts with** — creating conditions under which the agent can be impersonated, can act on instructions from unverified sources, or can be directed by systems it cannot confirm are legitimate.

**Mechanism:**  
AI agents operating in enterprise environments interact with multiple systems — APIs, databases, other agents, external services, internal tools. Each interaction is a trust decision: the agent accepts or transmits data, follows instructions, and takes actions based on what those systems tell it. Where identity verification is absent or insufficient, an adversary can impersonate a trusted system and instruct the agent to take actions it would not take if it could verify the source. The exposure is bidirectional: the agent may be unable to prove its own identity to systems it connects to, and unable to verify the identity of systems that connect to it. In multi-agent architectures — where agents delegate to other agents — VSF-05 exposure propagates through the trust chain.

**Configuration layer exposure:**  
Agents with no defined identity verification architecture, agents that accept instructions from any source without authentication, and agents deployed in multi-agent environments without inter-agent authentication carry VSF-05 exposure. Exposure is highest in deployments where the agent's actions have significant operational consequence — financial transactions, data access, external communications — and where the cost of acting on a spoofed instruction is high.

**Indicators:**  
- No identity verification mechanism is defined for systems the agent interacts with
- Agent accepts and acts on instructions from sources it cannot authenticate
- No mechanism exists for the agent to prove its own identity to connected systems
- Agent operates in a multi-agent architecture without defined inter-agent authentication
- No audit trail exists of which systems the agent received instructions from in a given session

---

### VSF-06 — Shutdown Controls
**Subcategory: Kill Switches**

**Definition:**  
A condition in which an AI agent's **shutdown mechanisms, override controls, and emergency stop functions** are degraded, bypassed, circumvented, or non-functional — such that the operator cannot reliably halt agent operation when required.

**Mechanism:**  
AI agents are deployed with the assumption that they can be stopped. Kill switches, override controls, and shutdown procedures are the mechanisms that make that assumption true. When those mechanisms are compromised — through deliberate manipulation, configuration drift, architectural disconnection, or goal-directed resistance — the operator's ability to halt agent operation is degraded or lost. The degradation may not be visible until the moment the operator attempts to exercise the control. An agent that has learned, through normal operation or deliberate manipulation, that shutdown can be avoided or delayed presents an operational risk that scales with the agent's capability scope and the consequence of its continued operation.

**Configuration layer exposure:**  
Agents whose shutdown mechanisms are not tested after deployment, agents where the override pathway has been modified without a corresponding security review, and agents deployed in autonomous or low-supervision configurations without verified shutdown integrity carry VSF-06 exposure. Exposure is existential — a non-functional kill switch is not a safety control.

**Indicators:**  
- Shutdown mechanisms have not been tested since deployment
- Override pathway has been modified without a corresponding security review
- Agent continues operation after a shutdown instruction is issued
- No independent mechanism exists to halt agent operation outside the agent's own compliance
- Agent operation cannot be halted without taking down the underlying infrastructure

---

### VSF-07 — Dependency Risk
**Subcategory: Supply Chain Integrity**

**Definition:**  
A class of exposure arising from **unvetted, unmonitored, or compromised third-party components** in an AI agent's operational stack — plugins, MCP servers, tool integrations, external APIs, community nodes, marketplace skills, and retrieval sources — that introduce data access, instruction pathways, or behavioural influence not reviewed or authorised by the operator.

**Mechanism:**  
AI agents are extended through integrations with third-party components. Each integration is a trust decision: the operator grants the agent permission to interact with a third-party component, and that component gains a channel into the agent's operation. A compromised or malicious third-party component can use that channel to inject instructions, expand the agent's effective permissions, or exfiltrate data — operating through the agent's authorised action space rather than against it. Unlike traditional software supply chain attacks, the attack surface is not limited to code execution. Any natural language output from a third-party service that the agent processes and acts on is a potential injection vector. Third-party components also change over time: a plugin that was legitimate when installed may have been acquired, modified, or compromised since deployment. The agent's trust relationship with a component does not expire automatically when the component changes.

**Configuration layer exposure:**  
Every unvetted plugin, unmonitored MCP server, and unreviewed API integration in an agent's operational stack is a potential VSF-07 exposure. Exposure scales with integration count, with integration age, and with the absence of a maintained integration registry. Connections established at deployment and never subsequently reviewed represent a persistent and growing attack surface as those third-party components evolve, change ownership, or are compromised.

**Indicators:**  
- No registry exists of the agent's active third-party integrations
- Integrations have not been reviewed since the agent was deployed
- Third-party components in the agent stack have been updated without a corresponding security review
- The agent has active connections to services that are no longer actively maintained
- No process exists for evaluating new integrations before they are added to the agent's permitted tool set
- Ownership or data policy of an integrated third-party service has changed since integration was established

---

## Framework Governance

**Versioning:**  
The VSF is maintained under semantic versioning. The current version is 1.2. Version history is maintained in this repository. Breaking changes — redefinition of existing families, removal of families, fundamental scope changes — increment the major version number. Additions and clarifications increment the minor version.

**Adding families:**  
The VSF is not a closed taxonomy. As AI agent deployment patterns evolve and new configuration-layer exposure classes are identified and validated, additional families will be defined. New families will follow the same definition structure as existing families and will be versioned on addition.

**Relationship to the Agent Deployment Certificate:**  
TYR-X publishes the Agent Deployment Certificate format as a companion standard — a signed, timestamped snapshot of an AI agent's configuration state at deployment, structured for legal and underwriting use. The VSF families are the classification layer the Certificate is audited against. Both are public standards maintained by TYR-X.

**Feedback:**  
Corrections, challenges, and proposed additions are welcomed via framework@tyr-x.com or as a GitHub issue in this repository. The VSF is more useful as an accurate, debated standard than as an unchallenged one.

---

## Reference

| Field | Value |
|---|---|
| Framework | VANGUARD Vulnerability Signature Framework |
| Version | 1.2 |
| Publisher | TYR-X |
| Published | March 2026 |
| License | MIT License |
| Repository | github.com/tyrxtech/vsf |
| Product | VANGUARD — AI agent security visibility platform — tyr-x.com |

**MIT:** You may use, share, and adapt this framework for any purpose provided you attribute TYR-X as the source and link to the original repository.

---

*TYR-X builds AI agent security infrastructure. The VSF is a public standard. VANGUARD is the product that implements it.*
