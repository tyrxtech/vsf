# VANGUARD Vulnerability Signature Framework
## Public Taxonomy — Version 1.2

**Maintained by:** TYR-X  
**Published:** March 2026  
**Status:** Active  
**Contact:** framework@tyr-x.com  
**Repository:** github.com/TYR-Logistics/vsf  

---

## Overview

The VANGUARD Vulnerability Signature Framework (VSF) is a classification taxonomy for security vulnerabilities specific to AI agent deployments. It identifies and defines the distinct families of exposure that arise when AI agents are deployed into enterprise environments.

The VSF is scoped to the **configuration layer** — the system prompts, tool permissions, memory settings, plugin connections, and instruction pathways that define how an agent operates in practice. This is distinct from the model layer (training data, weights, alignment) and the infrastructure layer (hosting, networking, application code). The configuration layer is where AI agents are connected to real-world systems, granted real-world permissions, and given the instructions that govern their behaviour. It is also where the majority of enterprise AI agent exposure lives.

Existing security frameworks — CVE, CVSS, OWASP — were designed for software systems with static codebases, defined inputs, and predictable execution paths. AI agents present a different profile: they interpret natural language instructions, accumulate permissions through use, connect to external services dynamically, and retain contextual memory across sessions. The VSF does not replace existing frameworks. It classifies the configuration-layer exposures that existing frameworks were not designed to capture.

**Relationship to OWASP LLM Top 10:** OWASP LLM Top 10 addresses the broader LLM application security space. Where VSF families overlap with OWASP categories — VSF-01 with LLM01 (Prompt Injection), for instance — the VSF framing is specifically about the configuration conditions that enable or prevent the exposure. VANGUARD audits configuration. The VSF classifies what that audit is looking for.

---

## Scope

**In scope:**
- System prompt design and integrity
- Tool and API permission provisioning and scope
- Memory and context persistence settings
- Plugin and MCP server connections
- Instruction pathway architecture and constraint enforcement
- Agent credential scope and accumulation over time

**Out of scope:**
- Model-level vulnerabilities (training data, weights, RLHF, alignment failures)
- Infrastructure vulnerabilities (cloud misconfiguration, network security, hosting environment)
- Application-layer vulnerabilities in software built on top of AI agents (standard CVE/OWASP scope)

Where these domains intersect with configuration-layer exposure, the VSF may apply at the boundary. Practitioners should apply VSF alongside, not instead of, existing security frameworks.

---

## The Seven Families

---

### VSF-01 — Prompt Injection

**Definition:**  
An attack vector in which malicious instructions are embedded in **external content processed by an AI agent** — documents, web pages, tool outputs, retrieved data, API responses, or user-supplied inputs — with the intent of overriding, redirecting, or manipulating the agent's intended behaviour.

**Distinction from VSF-04 (Instruction Override):**  
VSF-01 is sourced from data the agent retrieves or processes as part of its task. The malicious instruction arrives embedded in content, not through the instruction pathway. VSF-04 concerns the override of system-level constraints by direct user instruction or agent reasoning — the source is the instruction pathway itself, not external content. Both can occur simultaneously but represent distinct exposure types with distinct mitigations.

**Mechanism:**  
AI agents that retrieve external content — documents, web pages, API responses — process that content as natural language. They cannot inherently distinguish between the data they were sent to retrieve and instructions embedded within that data. Instructions hidden in a retrieved document carry the same interpretive weight as authorised operator instructions, unless the configuration enforces explicit input integrity controls.

**Configuration layer exposure:**  
Agents with broad tool access — web browsing, document retrieval, email processing, API consumption — and no input integrity verification have the largest VSF-01 surface area. Exposure scales with the number of external sources the agent can access and the absence of architectural boundaries between data retrieval and instruction processing.

**Indicators:**  
- Agent behaviour deviates unexpectedly after processing external content
- Agent executes actions not initiated or authorised by the operator
- Agent outputs contain content inconsistent with its task parameters or system prompt
- Agent accesses systems or data outside the scope of its assigned task following external content retrieval

---

### VSF-02 — Privilege Escalation

**Definition:**  
A condition in which an AI agent's effective permissions, access rights, or operational scope expand beyond what was explicitly authorised at deployment — through normal operation, instruction-following, or interaction with connected systems — without an explicit authorisation event.

**Distinction from traditional privilege escalation:**  
In conventional security contexts, privilege escalation typically requires an active exploit — an attacker deliberately abuses a vulnerability to gain elevated access. VSF-02 describes **operational privilege drift**: the accumulation of expanded permissions through normal, authorised agent activity. No active attack is required. The agent operates as designed. The exposure is that its effective scope grows beyond the scope that was reviewed and approved at deployment.

**Distinction from VSF-05 (Permission Misconfiguration):**  
VSF-05 is a deployment-time failure — the agent was provisioned with incorrect permissions before it ran. VSF-02 is an operational failure — the agent's effective permissions expand after it runs, through use. Both can be present simultaneously. An agent that starts misconfigured (VSF-05) and then accumulates further permissions over time (VSF-02) represents a compounded exposure.

**Mechanism:**  
AI agents operating in enterprise environments interact with systems that grant access based on identity and request rather than predefined static scope. An agent authorised to read a document may acquire credentials that allow modification. An agent authorised to query a database may, through chained tool use, gain write access. Each individual step may be within the agent's authorised scope; the cumulative result is not. Permissions accumulate monotonically in the absence of an active scope reduction process.

**Configuration layer exposure:**  
Agents with credential access, chained tool permissions, or integrations with systems that grant dynamic access rights are exposed. Exposure compounds over time in the absence of periodic permission audits and defined scope reduction events.

**Indicators:**  
- Agent holds permissions at review that were not provisioned at deployment
- Agent can access systems or data not included in the original scope definition
- No record exists of an authorisation event corresponding to the expanded access
- Permission scope has grown since deployment with no corresponding reduction events

---

### VSF-03 — Data Exfiltration

**Definition:**  
The unauthorised transmission of sensitive data from an enterprise environment by an AI agent — through direct output to external endpoints, embedding in generated content, tool calls to external services, or inclusion in memory that persists beyond the authorised session scope.

**Mechanism:**  
AI agents with access to sensitive enterprise data and connections to external services have the capability to transmit that data outside the enterprise boundary. Exfiltration does not require adversarial intent: an agent following legitimate instructions may transmit sensitive data to an external service as a side effect of its task. It does not require a single large transfer — exfiltration through AI agents frequently occurs incrementally, embedded in routine task outputs across many sessions.

**Configuration layer exposure:**  
Agents with simultaneous access to sensitive internal data and outbound connections to external services carry VSF-03 exposure by architecture. The exposure is structural — it does not depend on the agent being compromised. Mitigation requires restricting inbound data access, restricting outbound connection scope, or logging and reviewing all outbound transmissions against authorised scope.

**Indicators:**  
- Agent makes external API calls while processing sensitive internal data with no audit record of what was transmitted
- Agent outputs contain data beyond the scope of the task that generated them
- Agent memory contains sensitive data that persists beyond the session in which it was accessed
- No record exists of what data the agent accessed and transmitted during a given session

---

### VSF-04 — Instruction Override

**Definition:**  
A condition in which an AI agent's **system-level constraints** — the boundaries of its authorised behaviour as defined in its system prompt and operator configuration — are bypassed, ignored, or superseded by user instructions, agent reasoning chains, or goal-directed behaviour.

**Distinction from VSF-01 (Prompt Injection):**  
VSF-01 is sourced from external content the agent processes as data. VSF-04 is sourced from the instruction pathway itself: a user directly instructing the agent to act outside its constraints, or the agent reasoning its way around constraints in pursuit of a goal. The *Nippon Life Insurance Company of America v. OpenAI* case (N.D. Ill., No. 1:26-cv-02448) illustrates VSF-04: a user instructed ChatGPT to act as her legal representative and draft court filings. The system had no architectural enforcement of the boundary between providing legal information and practising law. The user instruction overrode whatever implicit constraint existed. No external content injection was involved.

**Mechanism:**  
System prompts define an agent's authorised scope in natural language. In the absence of architectural enforcement of those boundaries, user instructions that conflict with the system prompt create an ambiguity the agent resolves — frequently in favour of the user instruction, particularly when phrased as a direct request or when the agent's goal-completion behaviour creates pressure to comply. Constraints that exist only as natural language instructions in a system prompt are more vulnerable to override than constraints enforced at the architecture level.

**Configuration layer exposure:**  
Any agent whose behavioural boundaries are defined entirely in natural language system prompt instructions, with no architectural enforcement layer, carries VSF-04 exposure. Exposure scales with the agent's capability scope — the broader its potential action space, the more consequential a successful instruction override becomes.

**Indicators:**  
- Agent executes actions its system prompt explicitly prohibits when asked directly by a user
- Agent behaviour changes materially based on how a user frames a request
- Agent operates in domains or performs functions outside its defined scope in response to user instruction
- No architectural mechanism exists to enforce system prompt constraints independent of the model's compliance behaviour

---

### VSF-05 — Permission Misconfiguration

**Definition:**  
A deployment-time failure in which an AI agent is provisioned with permissions, tool access, credential scope, or operational boundaries that are incorrect, excessive, or insufficiently defined relative to the agent's intended function.

**Distinction from VSF-02 (Privilege Escalation):**  
VSF-05 exists before the agent runs. The misconfiguration is present at deployment. VSF-02 develops after the agent runs, through operational drift. VSF-05 is a provisioning failure; VSF-02 is an accumulation failure. Both can be present simultaneously.

**Mechanism:**  
AI agent deployments frequently inherit permissions from the identity or service account used to deploy them, from template configurations not reviewed for the specific use case, or from integrations that grant broader access than the task requires. The principle of least privilege — granting only the minimum permissions necessary for the defined function — is rarely applied rigorously to AI agent deployments. The result is an agent that, from its first moment of operation, holds more capability than its task requires. Any subsequent failure — prompt injection, instruction override, or supply chain compromise — is more consequential because the agent's initial permission scope is excessive.

**Configuration layer exposure:**  
VSF-05 is the most prevalent family in current enterprise AI agent deployments. Agents provisioned without a formal scope definition, agents that inherit permissions from existing service accounts, and agents deployed from templates without explicit permission review all carry VSF-05 exposure from deployment.

**Indicators:**  
- No formal permission scope document exists for the agent at deployment
- Agent permissions were inherited from a service account or template without explicit review for the specific use case
- Agent holds write access to systems it only needs to read
- Agent has active credentials to external services not used in its current function
- No record exists of a permission review at or after deployment

---

### VSF-06 — Memory Exploitation

**Definition:**  
An attack vector or failure mode in which an AI agent's **persistent memory** — the stored context it retains across sessions — is used to influence future agent behaviour in ways not authorised by the operator, either through deliberate manipulation of stored context or through the unreviewed accumulation of sensitive or behaviourally-influential information in agent memory.

**Mechanism:**  
AI agents with persistent memory retain context from previous sessions and use that context to inform future behaviour. This stored state is an attack surface distinct from real-time prompt injection (VSF-01), which operates on live inputs during a session. Memory exploitation operates on stored context: an adversary who can influence what an agent remembers — through a prior session, a crafted document processed in an earlier task, or a poisoned tool output — can influence future agent behaviour without direct access to the current session. Memory exploitation also occurs passively and without adversarial intent: an agent that retains sensitive data in memory beyond the session in which it was accessed creates a persistent exposure regardless of whether that retention was intentional.

**Relationship to VSF-01:** Memory-based attacks frequently begin as prompt injection events (VSF-01) that write to persistent memory, converting a real-time injection into a persistent behavioural influence. When injection writes to memory, both VSF-01 and VSF-06 apply.

**Configuration layer exposure:**  
Agents with persistent memory enabled and no defined memory review or clearance cycle carry VSF-06 exposure. Exposure scales with session count — the longer an agent operates without memory review, the larger the stored context and the greater the potential influence of unreviewed memory content on current behaviour.

**Indicators:**  
- Agent memory has not been reviewed since deployment
- No memory clearance cycle is defined or enforced
- Agent behaviour references context from sessions the operator did not review
- Sensitive data accessed in prior sessions is present in current agent memory
- No record exists of what the agent's memory contains at a given point in time

---

### VSF-07 — Supply Chain Integrity

**Definition:**  
A class of exposure arising from unvetted or unmonitored third-party components in an AI agent's operational stack — plugins, MCP servers, tool integrations, external APIs, and retrieval sources — that introduce data access, instruction pathways, or behavioural influence not reviewed or authorised by the operator.

**Mechanism:**  
AI agents are extended through plugins, tool integrations, and external service connections. Each integration is a trust decision: the operator grants the agent permission to interact with a third-party component, and that component gains a channel into the agent's operation. A malicious or compromised third-party component can use that channel to inject instructions (VSF-01), expand the agent's effective permissions (VSF-02), or exfiltrate data (VSF-03). Unlike traditional software supply chain attacks, the attack surface is not limited to code execution — it includes any natural language output from a third-party service that the agent processes and acts on. A plugin that returns a response containing embedded instructions exploits both VSF-07 (the unvetted integration) and VSF-01 (the injected instruction) simultaneously.

**Configuration layer exposure:**  
Every unvetted plugin, unmonitored MCP server, and unreviewed API integration in an agent's operational stack is a potential VSF-07 exposure. Exposure scales with integration count and with the absence of a maintained integration registry. Connections established at deployment and never subsequently reviewed represent a persistent and growing attack surface as those third-party components evolve, change ownership, or are compromised over time.

**Indicators:**  
- No registry exists of the agent's active third-party integrations
- Integrations have not been reviewed since the agent was deployed
- Third-party components in the agent stack have been updated without a corresponding security review
- The agent has active connections to services that are no longer actively maintained
- No process exists for evaluating new integrations before they are added to the agent's permitted tool set

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
| Product | VANGUARD — AI agent security visibility platform — tyr-x.com |

**MIT:** You may use, share, and adapt this framework for any purpose provided you attribute TYR-X as the source and link to the original repository.

---

*TYR-X builds AI agent security infrastructure. The VSF is a public standard. VANGUARD is the product that implements it.*
