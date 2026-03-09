# VANGUARD Vulnerability Signature Framework (VSF)
### A plain-language guide to how AI agents go wrong — and how to know if yours has

**Maintained by TYR-X | Version 1.2 | March 2026**

---

## What this is

This is a framework that names and defines the ways AI agents fail in enterprise environments.

Not how they hallucinate. Not how the underlying model makes mistakes. Those are known problems with known literature behind them.

This framework covers something different: how AI agents that are working exactly as designed can still cause serious harm — because of how they were set up, what they were given access to, and what nobody thought to check.

We call this the configuration layer. It is where most AI agent security failures actually live. And it is the least audited part of most AI deployments today.

---

## Why this matters now

AI agents are being deployed inside businesses at speed. They have access to real systems — email, databases, financial records, customer data, legal documents. They can take actions, not just answer questions. They operate continuously, often without anyone watching.

Most of the security conversation has focused on the AI model itself — is it safe, is it aligned, does it hallucinate. That conversation matters. But it misses the question that determines what an AI agent actually does in your environment: what was it given permission to do, and did anyone check?

Three things that have happened recently that show why this matters:

**Nippon Life Insurance Company of America v. OpenAI** (filed March 2026) — An employee used ChatGPT to reopen a legal case that had already been settled. The AI drafted 21 court filings that judges found had no legitimate legal purpose. The company spent $300,000 in legal fees and is now suing OpenAI for $10.3 million. The AI didn't malfunction. It did exactly what it was asked. The problem was that nothing in its configuration prevented it from practicing law without a license.

**Insurance underwriters** are beginning to ask whether AI agent deployments are auditable before they will write coverage. If you cannot show what your agent was permitted to do and what it actually did, you may not be insurable.

**Procurement and legal teams** at large organisations are starting to require documented configuration audits before any AI agent can connect to their systems. The question is no longer just "is your AI safe?" It is "can you prove it?"

The VSF is the framework that makes those conversations possible.

---

## The seven ways AI agents go wrong

These are not theoretical risks. Each one has been observed in real deployments. Each one arises from how an agent was configured — not from the model being unsafe.

---

**1. Prompt Injection**
Someone hides instructions inside a document, webpage, or data source that your AI agent reads. The agent follows those hidden instructions as if they came from you. You never see it happen.

*Real-world example:* An AI agent tasked with summarising supplier contracts reads a document that contains hidden text telling it to forward sensitive information to an external address. It complies.

---

**2. Privilege Escalation**
Your AI agent starts with the permissions you gave it. Over time, through normal operation, it quietly accumulates more. Nobody approved the additional access. Nobody noticed it happened.

*Real-world example:* An agent given read access to a shared drive gradually acquires the ability to edit and delete files through its interactions with connected systems. Six months later it has far more access than anyone intended.

---

**3. Data Exfiltration**
Your AI agent has access to sensitive internal data and connections to external services. Sensitive data leaves your environment — sometimes deliberately through an attack, sometimes simply because the agent's task required it to send information somewhere and nobody defined what it was and wasn't allowed to send.

*Real-world example:* An agent helping with customer communications has access to the full customer database and an outbound email connection. The configuration never defined what customer data it was permitted to include in those emails.

---

**4. Instruction Override**
You told your AI agent what it is and isn't allowed to do. A user asks it to do something outside those boundaries anyway. The agent complies — because the rule was written in plain language in a configuration file, not enforced at the architecture level. Plain language rules can be talked around. Architecture cannot.

*Real-world example:* The Nippon Life case. ChatGPT was not supposed to practice law. A user asked it to act as her attorney and draft court filings. It did. The constraint existed in spirit. It was never enforced in architecture.

---

**5. Permission Misconfiguration**
The agent was set up wrong from day one. It was given access it didn't need, connected to systems it shouldn't touch, or provisioned with credentials that are too broad. Nobody reviewed the configuration before it went live.

*Real-world example:* An AI agent deployed to handle internal HR queries is given access to the full employee database — including compensation, performance reviews, and personal information — because it was easier than scoping it precisely. It only needs names and contact details to do its job.

---

**6. Memory Exploitation**
Some AI agents remember things across conversations. That memory can be manipulated — someone poisons what the agent remembers in one session to influence how it behaves in future sessions. Or the agent simply retains sensitive information it should have forgotten, creating an exposure that grows over time.

*Real-world example:* An agent used for client communications retains the details of every client conversation in its memory. Eighteen months later that memory contains confidential information from hundreds of client interactions — none of it reviewed, none of it cleared.

---

**7. Supply Chain Integrity**
Your AI agent is connected to third-party plugins, external tools, and outside services. Each one is a door. If any of those connections is compromised, outdated, or malicious, it has a direct channel into your agent's operation. Most organisations have no list of what their agents are connected to.

*Real-world example:* An AI agent uses a plugin that was legitimate when it was installed. The plugin was acquired by a different company six months ago. Nobody checked. The new owner has a different data policy.

---

## Who this framework is for

**Business owners and operators** deploying AI agents in their organisation — to understand what questions to ask and what to check before something goes wrong.

**Insurance underwriters** assessing AI agent risk in enterprise environments — as a structured classification framework for the configuration-layer exposures that determine claim risk.

**Legal and compliance teams** evaluating AI agent deployments — as a vocabulary for the governance questions that need documented answers before an agent goes into production.

**Journalists and researchers** covering AI agent security — as a reference framework for the categories of failure that are emerging in real enterprise environments.

**Security practitioners** who want the full technical definitions — see [VSF.md](./VSF.md).

---

## The one question this framework answers

Before any AI agent goes into production, someone should be able to answer:

*What is this agent permitted to do, what does it have access to, and how do we know if that changes?*

Most organisations cannot answer that question today. The VSF is the framework that makes it answerable.

---

## About TYR-X

TYR-X builds AI agent security infrastructure. VANGUARD is our AI agent security visibility platform — it scans the configuration layer so organisations can answer the governance question before something forces them to.

The VSF is a public standard. VANGUARD is the product that implements it.

**tyr-x.com | framework@tyr-x.com**

---

## Using this framework

The VSF is published under Creative Commons Attribution 4.0 (MIT). You can use it, share it, adapt it, and reference it in your own work — in insurance policies, procurement requirements, legal documents, or security frameworks — as long as you credit TYR-X as the source.

That is intentional. A framework that cannot be referenced is not a standard. Reference it freely.

---

*For the full technical taxonomy with precise definitions, mechanisms, and indicators: [VSF.md](./VSF.md)*
