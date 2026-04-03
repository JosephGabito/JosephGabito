Applied AI engineer building production-grade LLM systems, agents, and AI infrastructure.

I work at the boundary of product, architecture, and execution: turning ambiguous business problems into systems that are reliable, observable, and useful in the real world. My focus is not research for its own sake. My focus is applied AI that ships.

## What I do

I design and build:

- Agentic systems with tool use, routing, and orchestration
- Durable streaming architectures that survive disconnects and resume cleanly
- Human-in-the-loop approval workflows with strong backend guarantees
- Provider-agnostic LLM infrastructure with cost, latency, and reliability controls
- Evaluation-aware systems that improve with feedback instead of vibes

My background is in backend architecture, distributed systems thinking, and product engineering. I care about correctness, maintainability, and making AI features survive contact with real users.

## Current focus

Right now I’m focused on building AI systems that are:

- reliable under failure
- durable across reconnects and dropped sessions
- safe around destructive actions
- measurable in cost, latency, and success rate
- practical for businesses, not just impressive in demos

## Selected engineering themes

### Durable agent execution
I build agent systems where the task is not tied to the browser tab, request lifecycle, or SSE connection. Execution continues independently, and clients can reconnect without losing the state of the run.

### Human-in-the-loop safety
For sensitive actions, I use explicit approval boundaries with atomic backend coordination. This avoids the usual race conditions and double-submit problems that show up in distributed environments.

### Provider-agnostic orchestration
Models are tools, not religion. I care about capability, latency, stability, and cost. I design systems that can route across providers, evaluate tradeoffs, and keep operating when one vendor degrades.

### Applied AI over hype
I’m interested in real systems: agents, workflows, evals, context handling, tool semantics, and production behavior. Not prompt theater.

## Tech stack

**Languages**  
Python, PHP, JavaScript, TypeScript, SQL

**Backend**  
FastAPI, Redis, Celery, MySQL, PostgreSQL, SQLAlchemy

**Frontend**  
Next.js, Lit, React

**AI / LLM**  
Anthropic APIs, OpenAI-compatible APIs, Vertex AI, OpenRouter, vLLM, Hugging Face, RAG pipelines, tool calling, structured outputs, eval loops

**Architecture**  
DDD, layered architecture, contract-first APIs, observability, failure-aware system design

## Principles

- Clarity over cleverness
- Reliability over demos
- Contracts over guesswork
- Systems that survive failure
- Small sharp abstractions over magic
- AI is infrastructure

## A few things I care about

I like building boring, mechanical systems that hold up under pressure.

I care a lot about architecture, domain boundaries, explicit contracts, and getting the fundamentals right. I’ve spent years building in messy environments, which taught me that elegant systems only matter if they can survive real traffic, real users, and real failure modes.

## Writing and open source

I use GitHub to share code, experiments, and systems thinking around:

- applied AI engineering
- agents and orchestration
- reliability patterns for LLM products
- backend architecture
- WordPress and product infrastructure

## Let’s connect

If you're working on applied AI, agents, product infrastructure, or production LLM systems, feel free to reach out.
