# Agents

Compliance Copilot uses three groups of agents. Each agent is one job given to a language model, with fixed
instructions, only the material it is allowed to see, and a form it must fill in. Code decides when each agent
runs and checks what it returns. Every agent call is recorded in the audit log.

- **For business owners:** an Orchestrator routes each question to domain agents, which answer only from their
  own regulators' laws with word-for-word quotes.
- **For the knowledge base:** agents draft obligations from the law and propose their order and scope; nothing
  reaches users until a person verifies it.
- **For regulators:** in the Incident Room, each institution has its own agent; a Coordinator compares their
  assessments and a Gap Analyst lists the gaps.

All agents use DeepSeek-V4.1-Flash. FAST mode answers directly; STRONG mode reasons first and keeps that reasoning
in the audit log.

## Core agents
| Agent | Group | What it does |
|---|---|---|
| Orchestrator | Owners | Reads each message and decides who should answer it |
| Domain agents | Owners | Answer from their own regulators' laws, with word-for-word quotes |
| Profiler | Owners | Turns the owner's description into facts about the business |
| Extractor | Knowledge base | Drafts obligations from a regulator's laws |
| Regulator agent | Regulators | Plays one institution in an Incident Room exercise |
| Coordinator | Regulators | Compares the regulators' assessments after each event |
| Gap Analyst | Regulators | Lists the gaps an exercise exposed |
| Policy & Legal Adviser | Regulators | Answers participants' questions during an exercise |

### Orchestrator
- **Receives:** the owner's message and the business's steps.
- **Returns:** the kind of message, one or two domain agents to answer it, and up to four searches in the law's wording.
- **Mode:** FAST.
- **Checked by:** an injection guard runs first; off-topic messages get a fixed reply.

### Domain agents
Each country defines its own five. Tanzania has Registration & licensing, Tax, Employment & workplace,
Standards, health, safety & environment, and Sector regulators. The UAE has Registration, licensing & companies,
Tax, Employment, Sector licences & regulated activities, and Consumers, fair trade & public health. Answers come
in the language of the question (English, Swahili or Arabic), with quotes in the law's own words.
- **Receives:** the question and sections from its own regulators' laws.
- **Returns:** up to four short points, each with a section and a word-for-word quote.
- **Mode:** FAST, or STRONG if an admin sets it to Thorough.
- **Checked by:** the Editor merges the points; the Verifier (code) checks every quote and figure.

### Profiler
- **Receives:** the owner's description and the country's profile questions.
- **Returns:** facts such as legal form and number of staff, each backed by the owner's own words, and a short name.
- **Mode:** FAST.
- **Checked by:** code validates each fact and checks its quote; required facts the description left out are asked.

### Extractor
- **Receives:** the sections of a regulator's laws that create obligations.
- **Returns:** draft obligations (who must do what, when, with what penalty), each claim quoted from the law.
- **Mode:** STRONG.
- **Checked by:** code checks every quote; each obligation stays a draft until an expert verifies it.

### Regulator agent
- **Receives:** only the events sent to its institution, messages from other agents, and sections of its own laws.
- **Returns:** an assessment: whether the event is within its mandate, the legal basis with quotes, actions,
  uncertainties, and messages to other agents.
- **Mode:** FAST.
- **Checked by:** the same quote and figure checks as the advisor.

### Coordinator
- **Receives:** all the assessments for an event.
- **Returns:** a lead institution or "unclear", conflicts, and joint actions.
- **Mode:** FAST.
- **Checked by:** it adds no legal facts of its own; it only compares what the agents said.

### Gap Analyst
- **Receives:** the whole exercise and the sections cited in it.
- **Returns:** two to six gaps, each with a type, a severity and cited evidence.
- **Mode:** STRONG.
- **Checked by:** code checks its quotes; an admin reviews each finding.

### Policy & Legal Adviser
- **Receives:** a participant's question, the exercise so far, and relevant sections.
- **Returns:** a short answer with cited sections.
- **Mode:** FAST.
- **Checked by:** the same quote and figure checks as the advisor.

## Supporting agents
| Agent | What it does |
|---|---|
| Editor | Merges the domain agents' points into one short answer, reusing only their quotes |
| Profile-change agent | Proposes updates when an owner mentions a change; the owner confirms |
| Query planner | Turns a question in any wording or language into searches in the law's wording |
| Scout | Finds candidate documents on official sites for the human review queue |
| Linker | Proposes which obligation must come first, with a reason |
| Scoper | Proposes which businesses an obligation applies to; an admin decides |
