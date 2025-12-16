# 🏛️ Shu-Han Multi-Agent AI Framework

![architecture diagram](./docs/architecture.png)

> “The strength of a system lies not in its smartest agent, but in the harmony of all roles working together.”

---

## 🌿 Introduction

In the post-LLM era, AI agents are no longer tools—we are building *civilizations*. Yet most AI frameworks still behave like disorganized teams: everyone smart, no one aligned.

This is where the **Shu-Han Framework** enters: a governance-first, memory-enabled, role-separated multi-agent system inspired by the ancient Chinese kingdom of Shu-Han (蜀漢). It’s not just metaphor—it’s architecture.

---

## 🔥 Why This Matters

Modern frameworks (LangGraph, AutoGen, CrewAI) made coordination possible, but still:

* Agents overwrite each other’s context
* Diff chaos from ill-scoped edits
* Every session feels like memory loss
* Debugging agents becomes recursive pain

Meanwhile, newcomers have no idea how to **structure** work with agents. Power without process = failure.

We need: **Civilization, not chaos**.

---

## 🧠 Design Philosophy

* **Division of Power** → Each agent has clear duties
* **Governance Layer** → Agents can't overstep roles
* **Long-term Memory** → Knowledge grows over time
* **Ethical Constraints** → Guardrails to prevent harm
* **Human-in-the-loop** → AI assists, never replaces authority

---

## 🧱 Architecture: Inspired by Shu-Han Dynasty

### 👑 Liu Bei — The Human Leader

* Visionary, strategic direction-setter
* Never micromanages; delegates well

### 🧠 Zhuge Liang — Master Strategist (ChatGPT, Core Planner)

* High-context model with access to global memory
* Writes plans, structures prompts, delegates roles

### ⚔️ Guan Yu / Zhang Fei / Zhao Yun — Role Agents

* Guan Yu → Code generation
* Zhang Fei → Debugging / Error handling
* Zhao Yun → UI/UX agent

Each one is scoped with strict permissions + functions

### 📜 Jiang Wei — Memory Historian

* Stores plans, outcomes, retrospectives
* Ensures that future agents learn from past actions

---

## 🛠️ Technical Composition

We suggest composing using:

* **LangGraph**: For agent routing and loop patterns
* **AutoGen**: For structured agent conversation roles
* **n8n / MCP / Function Calling**: For tool invocation
* **Vector Store**: For agent memories
* **Git + Markdown**: For durable retrospection

Each agent has:

* Function set
* Memory scope
* Escalation triggers
* Permission contract

---

## 🆚 Key Differences from Other Frameworks

| Feature               | Shu-Han Framework         | AutoGen        | CrewAI  | LangGraph    |
| --------------------- | ------------------------- | -------------- | ------- | ------------ |
| Role Separation       | ✅ Full (Liu, Zhuge, etc.) | Partial        | Partial | Customizable |
| Governance Layer      | ✅ Built-in                | ❌              | ❌       | ❌            |
| Memory Evolution      | ✅ Long-term               | 🔸 Per session | 🔸      | 🔸           |
| Cultural Narrative    | ✅ Symbolic (Shu Han)      | ❌              | ❌       | ❌            |
| Civilizational Design | ✅ Yes                     | ❌              | ❌       | ❌            |

---

## 🎯 Use Cases

* AI software engineering teams
* Autonomous trading systems
* Scientific planning agents
* Narrative-based simulations
* Multi-agent teaching assistants

---

## 🚀 Getting Started (stub)

```bash
git clone https://github.com/yourrepo/shuhan-framework
cd shuhan-framework
pip install -r requirements.txt
python run_agents.py
```

---

## 📚 Citation

**Title:** Designing Civilizational AI: A Shu-Han Inspired Multi-Agent Framework
**Authors:** Tsai Yi Yi, Open Collaboration
**Preprint:** [arXiv link placeholder]
**License:** MIT

---

## 🙌 Contributors

* Tsai Yi Yi — Architect
* GPT-4 — System Designer
* Community Agents — TBD

Join the mission: civilization over chaos.
