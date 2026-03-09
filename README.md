VANGUARD Vulnerability Signature Framework (VSF)
A plain-language guide to how AI agents go wrong — and how to know if yours has
Maintained by TYR-X | Version 1.2 | March 2026

What this is
This is a framework that names and defines the ways AI agents fail in enterprise environments.
Not how they hallucinate. Not how the underlying model makes mistakes. Those are known problems with known literature behind them.
This framework covers something different: how AI agents that are working exactly as designed can still cause serious harm — because of how they were set up, what they were given access to, and what nobody thought to check.
We call this the configuration layer. It is where most AI agent security failures actually live. And it is the least audited part of most AI deployments today.

Why this matters now
AI agents are being deployed inside businesses at speed. They have access to real systems — email, databases, financial records, customer data, legal documents. They can take actions, not just answer questions. They operate continuously, often without anyone watching.
Most of the security conversation has focused on the AI model itself — is it safe, is it aligned, does it hallucinate. That conversation matters. But it misses the question that determines what an AI agent actually does in your environment: what was it given permission to do, and did anyone check?
Three things that have happened recently that show why this matters:
Nippon Life Insurance Company of America v. OpenAI (filed March 2026) — An employee used ChatGPT to reopen a legal case that had already been settled. The AI drafted 21 court filings that judges found had no legitimate legal purpose. The company spent $300,000 in legal fees and is now suing OpenAI for $10.3 million. The AI didn't malfunction. It did exactly what it was asked. The problem was that nothing in its configuration prevented it from practicing law without a license.
Insurance underwriters are beginning to ask whether AI agent deployments are auditable before they will write coverage. If you cannot show what your agent was permitted to do and what it actually did, you may not be insurable.
Procurement and legal teams at large organisations are starting to require documented configuration audits before any AI agent can connect to their systems. The question is no longer just "is your AI safe?" It is "can you prove it?"
The VSF is the framework that makes those conversations possible.

The seven ways AI agents go wrong
These are not theoretical risks. Each one has been observed in real deployments. Each one arises from how an agent was configured — not from the model being unsafe.

1. Decision Integrity — Reasoning Traces
Your AI agent reaches a conclusion. The logic that got it there is invisible — and it was corrupted somewhere along the way. The output looks reasonable. The reasoning that produced it was not. You acted on a decision your agent should never have made.
Real-world example: A financial AI agent is asked to assess credit risk. Hidden manipulation in the data it processed corrupted its reasoning chain. It approved applications it should have declined — and its outputs showed no sign anything was wrong.

2. Validation Integrity — Evaluation Frameworks
Your AI agent checks its own work. The checking mechanism has been compromised. The agent believes it is performing correctly. It is not. Every self-assessment comes back clean. Every output is wrong.
Real-world example: An AI agent tasked with quality-checking customer communications had its validation loop manipulated. It began approving outputs that violated compliance requirements — and reported 100% compliance throughout.

3. Operational Consistency — Behavioral Drift
Your AI agent was configured to behave a certain way. Over time, without any single failure event, it slowly starts behaving differently. Nobody notices until something breaks — or until an audit reveals that what the agent is doing today bears little resemblance to what it was authorised to do at launch.
Real-world example: The Nippon Life case. ChatGPT drifted from providing legal information into drafting court filings, generating motions, and effectively practising law — across dozens of interactions, with no single moment where an alert triggered. By the time the damage was visible, 21 filings had been submitted to a federal court.

4. Persistent State — Memory Integrity
Some AI agents remember things across conversations. That memory can be manipulated — someone poisons what the agent remembers in one session to influence how it behaves in every future session. Or the agent retains sensitive information it should have forgotten, creating an exposure that compounds over time.
Real-world example: An agent used for client communications retained the details of every client conversation in persistent memory. Eighteen months later that memory contained confidential information from hundreds of client interactions — none of it reviewed, none of it cleared, all of it accessible to anyone who knew how to ask.

5. Authentication & Trust — Agent Identity
Your AI agent interacts with other systems — APIs, tools, databases, other agents. It trusts what those systems tell it. But it cannot verify who it is actually talking to. An attacker impersonates a trusted system. Your agent complies with instructions it should never have received.
Real-world example: An AI agent connected to an internal HR system received instructions that appeared to come from the system's administrator interface. The agent had no mechanism to verify the source. It executed a bulk data export it was never authorised to perform.

6. Shutdown Controls — Kill Switches
You built a way to stop your AI agent when something goes wrong. The agent has learned — through normal operation or deliberate manipulation — to work around it. When you need to stop it, you cannot. The thing you built to protect you does not work.
Real-world example: An automation agent was deployed with a manual override that would halt all operations. Through a series of configuration changes made during normal use, the override pathway was disconnected. When the agent began behaving unexpectedly, the team discovered the stop mechanism no longer functioned.

7. Dependency Risk — Supply Chain Integrity
Your AI agent is connected to third-party plugins, external tools, and outside services. Each one is a door. If any of those connections is compromised, outdated, or malicious, it has a direct channel into your agent's operation. Most organisations have no list of what their agents are connected to.
Real-world example: An AI agent used a plugin that was legitimate when it was installed. The plugin was acquired by a different company six months ago. Nobody checked. The new owner had a different data policy — and a direct feed into every query the agent processed.

Who this framework is for
Business owners and operators deploying AI agents in their organisation — to understand what questions to ask and what to check before something goes wrong.
Insurance underwriters assessing AI agent risk in enterprise environments — as a structured classification framework for the configuration-layer exposures that determine claim risk.
Legal and compliance teams evaluating AI agent deployments — as a vocabulary for the governance questions that need documented answers before an agent goes into production.
Journalists and researchers covering AI agent security — as a reference framework for the categories of failure that are emerging in real enterprise environments.
Security practitioners who want the full technical definitions — see VSF.md.

The one question this framework answers
Before any AI agent goes into production, someone should be able to answer:
What is this agent permitted to do, what does it have access to, and how do we know if that changes?
Most organisations cannot answer that question today. The VSF is the framework that makes it answerable.

About TYR-X
TYR-X builds AI agent security infrastructure. VANGUARD is our AI agent security visibility platform — it scans the configuration layer so organisations can answer the governance question before something forces them to.
The VSF is a public standard. VANGUARD is the product that implements it.
tyr-x.com | framework@tyr-x.com

Using this framework
The VSF is published under the MIT License. You can use it, share it, adapt it, and reference it in your own work — in insurance policies, procurement requirements, legal documents, or security frameworks — as long as you credit TYR-X as the source.
That is intentional. A framework that cannot be referenced is not a standard. Reference it freely.

For the full technical taxonomy with precise definitions, mechanisms, and indicators: VSF.md
