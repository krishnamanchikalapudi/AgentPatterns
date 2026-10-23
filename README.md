# AI Agent Architecture Patterns

AI agents shift AI from passive text generators into active problem-solvers that reason, plan, and execute multi-step tasks independently.

> **Deterministic first, agentic where needed. Every pattern justified by the Scorecard.**

Use a deterministic workflow when rules are known. Use a single agent + tools when interpretation is needed. Add RAG + memory when grounding is needed. Add multi-agent only when specialization improves measured outcomes. Run autonomous long-running agents only with durable state, budgets, verification, and stop controls.

> What measurable improvement in task success, cycle time, reliability, or cost justifies adding another agent vs. keeping it simpler?

## Evaluation Scorecard

| Dimension | Metrics |
|---|---|
| Business value | Task completion, resolution rate, cycle time, CSAT |
| Quality | Evidence-grounded rate, tool accuracy, correction rate |
| Reliability | Success rate, timeouts, duplicate side-effects, recovery |
| Performance | p50/p95 latency, time to first result |
| Cost | Cost/task, tokens/task, tool calls/task |
| Security | Unauthorized actions, data incidents, approval compliance |
| Maintainability | Change-failure rate, regression escapes, MTTD, rollback time |

Fan-out cost grows O(n) to O(n²) depending on topology. Measure actuals.

## Adoption Sequence

**P0: Safety + Tools → P1: Reasoning/Planning/Orchestration/Reliability → P2: RAG/Memory/Eval → P3: Multi-Agent → P4: Advanced**

---

## P0: Safety (must have)

- **Human-in-the-Loop** — Gate irreversible or high-risk actions behind explicit human approval.
  - Ref: [Constitutional AI, arXiv:2212.08073](https://arxiv.org/abs/2212.08073)
- **Risk-Tiered Autonomy** — Scale agent freedom to action risk (read < write < spend/delete).
  - Ref: [AgentDojo, arXiv:2406.13352](https://arxiv.org/abs/2406.13352)
- **Least-Privilege Tooling + Identity Propagation + Secrets Isolation** — Agents act only with the caller's scoped identity; secrets never enter model context.
  - Ref: [Defeating Prompt Injections by Design (CaMeL), arXiv:2503.18813](https://arxiv.org/abs/2503.18813)
- **Sandboxed Execution & Runtime Isolation** — Execute untrusted code, shell commands, and browser sessions inside isolated containers/microVMs with strict filesystem and network egress boundaries.
  - Ref: [SWE-agent, arXiv:2405.15793](https://arxiv.org/abs/2405.15793)
  - Ref: [OSWorld, arXiv:2404.07972](https://arxiv.org/abs/2404.07972)
- **Policy-as-Code + Guardrails** — Enforce rules outside the prompt, in programmable and testable rails.
  - Ref: [NeMo Guardrails, arXiv:2310.10501](https://arxiv.org/abs/2310.10501)
  - Ref: [Llama Guard, arXiv:2312.06674](https://arxiv.org/abs/2312.06674)
- **Prompt-Injection Defense** — Treat all retrieved/tool content as untrusted data, never as instructions.
  - Ref: [Indirect Prompt Injection, arXiv:2302.12173](https://arxiv.org/abs/2302.12173)
  - Ref: [The Instruction Hierarchy, arXiv:2404.13208](https://arxiv.org/abs/2404.13208)
  - Ref: [Spotlighting, arXiv:2403.14720](https://arxiv.org/abs/2403.14720)
- **Audit Trace** — Log every decision, tool call, and approval for replay and forensics.

## P0: Tools

- **Tool Use** — Let the model call external APIs/functions to act and fetch facts.
  - Ref: [Toolformer, arXiv:2302.04761](https://arxiv.org/abs/2302.04761)
  - Ref: [Gorilla, arXiv:2305.15334](https://arxiv.org/abs/2305.15334)
  - Ref: [ToolLLM, arXiv:2307.16789](https://arxiv.org/abs/2307.16789)
- **CodeAct** — Use executable code as the unified action space.
  - Ref: [CodeAct, arXiv:2402.01030](https://arxiv.org/abs/2402.01030)
- **Agent-Computer Interface (ACI) + Computer Use** — Design token-efficient, ergonomic action/observation wrappers (or GUI/browser automation) tailored to how models perceive state and recover from errors.
  - Ref: [SWE-agent (ACI), arXiv:2405.15793](https://arxiv.org/abs/2405.15793)
  - Ref: [OSWorld, arXiv:2404.07972](https://arxiv.org/abs/2404.07972)
  - Ref: [WebArena, arXiv:2307.13854](https://arxiv.org/abs/2307.13854)
- **Parallel Function Calling** — Run independent tool calls concurrently to cut latency.
  - Ref: [LLMCompiler, arXiv:2312.04511](https://arxiv.org/abs/2312.04511)
- **Contract-Grounded + Structured Output + Read-Validate-Write-Verify** — Validate schemas before and after every side-effecting call.
  - Ref: [CRITIC, arXiv:2305.11738](https://arxiv.org/abs/2305.11738)
- **MCP + A2A** — Standard protocols for tool access (MCP) and agent-to-agent interop (A2A).
  - Ref: [Model Context Protocol spec](https://modelcontextprotocol.io)
  - Ref: [A2A Protocol spec](https://a2a-protocol.org)

## P1: Core Reasoning

- **ReAct** — Interleave reasoning with tool actions and observations.
  - Ref: [arXiv:2210.03629](https://arxiv.org/abs/2210.03629)
- **Inference-Time Scaling / Test-Time Compute** — Allocate dynamic thinking budgets, search, and verification at inference time based on task difficulty rather than relying solely on larger pre-trained models.
  - Ref: [Scaling LLM Test-Time Compute, arXiv:2408.03314](https://arxiv.org/abs/2408.03314)
  - Ref: [DeepSeek-R1, arXiv:2501.12948](https://arxiv.org/abs/2501.12948)
  - Ref: [s1: Simple Test-Time Scaling, arXiv:2501.19393](https://arxiv.org/abs/2501.19393)
- **Self-Ask** — Decompose a question into sub-questions answered one at a time.
  - Ref: [arXiv:2210.03350](https://arxiv.org/abs/2210.03350)
- **Self-Consistency** — Sample multiple reasoning paths and take the majority answer.
  - Ref: [arXiv:2203.11171](https://arxiv.org/abs/2203.11171)
- **Self-Refine** — Generate, critique, and revise with the same model.
  - Ref: [arXiv:2303.17651](https://arxiv.org/abs/2303.17651)
- **Reflexion** — Store verbal lessons from failures to improve the next attempt.
  - Ref: [arXiv:2303.11366](https://arxiv.org/abs/2303.11366)
- **Self-Debug** — Use execution feedback to repair generated code.
  - Ref: [arXiv:2304.05128](https://arxiv.org/abs/2304.05128)
- **Tree of Thoughts** — Explore and backtrack across branching reasoning paths.
  - Ref: [arXiv:2305.10601](https://arxiv.org/abs/2305.10601)
- **Graph of Thoughts** — Merge, refine, and reuse thoughts as a graph.
  - Ref: [arXiv:2308.09687](https://arxiv.org/abs/2308.09687)
- **Evidence-Grounded + Stop-When-Sufficient + Ask-Before-Assuming** — Cite evidence, halt when enough, and clarify ambiguity instead of guessing.

## P1: Planning

- **Plan-and-Execute / Plan-and-Solve** — Plan first, then execute steps, replanning only on failure.
  - Ref: [Plan-and-Solve, arXiv:2305.04091](https://arxiv.org/abs/2305.04091)
  - Ref: [ReWOO, arXiv:2305.18323](https://arxiv.org/abs/2305.18323)
- **Hierarchical Decomposition (Least-to-Most)** — Break a hard task into ordered, simpler subtasks.
  - Ref: [Least-to-Most, arXiv:2205.10625](https://arxiv.org/abs/2205.10625)
  - Ref: [Chain-of-Thought, arXiv:2201.11903](https://arxiv.org/abs/2201.11903)
- **Dependency-Aware / Symbolic Planning** — Offload planning to a classical planner for validity guarantees.
  - Ref: [LLM+P, arXiv:2304.11477](https://arxiv.org/abs/2304.11477)
  - Ref: [LLM-Modulo, arXiv:2402.01817](https://arxiv.org/abs/2402.01817)
- **Parallel Planning** — Compile a task DAG and run independent steps concurrently.
  - Ref: [LLMCompiler, arXiv:2312.04511](https://arxiv.org/abs/2312.04511)
- **Plan Validation + Precondition-Action-Postcondition + Draft-Preview-Approve-Commit** — Check feasibility before acting and preview before committing.
  - Ref: [SayCan, arXiv:2204.01691](https://arxiv.org/abs/2204.01691)

## P1: Orchestration

- **Deterministic Workflow with Agentic Steps** — Fixed control flow, with LLM judgment only inside bounded nodes.
  - Ref: [Anthropic, Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents)
- **Prompt Chaining & Routing** — Sequence narrow LLM steps with programmatic gates, or classify inputs up front to dispatch to specialized prompts/models.
  - Ref: [Anthropic, Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents)
- **Supervisor Orchestrator** — One coordinator routes work to specialist workers.
  - Ref: [AutoGen, arXiv:2308.08155](https://arxiv.org/abs/2308.08155)
  - Ref: [Magentic-One, arXiv:2411.04468](https://arxiv.org/abs/2411.04468)
- **Evaluator-Optimizer** — A generator and a separate evaluator loop until criteria pass.
  - Ref: [Self-Refine, arXiv:2303.17651](https://arxiv.org/abs/2303.17651)
- **Fan-Out / Fan-In (Sectioning & Voting)** — Run parallel workers on independent subtasks or ensemble perspectives and aggregate results.
  - Ref: [Mixture-of-Agents, arXiv:2406.04692](https://arxiv.org/abs/2406.04692)
- **Iterative Deep Research (Scope-Plan-Search-Critique-Synthesize)** — Multi-step investigative loop that decomposes open-ended queries, executes parallel retrieval/browsing, verifies coverage gaps, and synthesizes cited reports.
  - Ref: [BrowseComp, arXiv:2504.12516](https://arxiv.org/abs/2504.12516)
  - Ref: [Agentic RAG Survey, arXiv:2501.09136](https://arxiv.org/abs/2501.09136)
- **Event-Driven + Queue + Durable + Checkpoint-Resume** — Persist state and progress artifacts across sessions so long-running tasks survive crashes, context resets, and human waits.
  - Ref: [MemGPT, arXiv:2310.08560](https://arxiv.org/abs/2310.08560)
- **Saga / Compensation + Human Escalation** — Undo partial side-effects on failure and escalate to a human.

## P1: Reliability

- **Timeouts, Retry Classification, Backoff/Jitter, Circuit Breaker, Idempotency, Durable Replay, Health Checks** — Standard distributed-systems hygiene applied to agent loops.
- **Hard Operational Budgets & Loop Breakers** — Enforce strict caps on steps, wall-clock time, token spend, and repeated identical tool calls to prevent runaway loops.
- **Fallback / Cascade Routing** — Try cheap models first and escalate only when needed.
  - Ref: [FrugalGPT, arXiv:2305.05176](https://arxiv.org/abs/2305.05176)
  - Ref: [RouteLLM, arXiv:2406.18665](https://arxiv.org/abs/2406.18665)

## P2: RAG & Memory

- **RAG** — Ground generation in retrieved documents.
  - Ref: [arXiv:2005.11401](https://arxiv.org/abs/2005.11401)
- **Agentic RAG / Self-RAG** — The model decides when to retrieve and critiques what it retrieved.
  - Ref: [Self-RAG, arXiv:2310.11511](https://arxiv.org/abs/2310.11511)
  - Ref: [Agentic RAG Survey, arXiv:2501.09136](https://arxiv.org/abs/2501.09136)
- **Corrective RAG** — Score retrieval quality and fall back to re-search when poor.
  - Ref: [arXiv:2401.15884](https://arxiv.org/abs/2401.15884)
- **GraphRAG** — Use knowledge graphs for multi-hop and global questions.
  - Ref: [GraphRAG, arXiv:2404.16130](https://arxiv.org/abs/2404.16130)
  - Ref: [HippoRAG, arXiv:2405.14831](https://arxiv.org/abs/2405.14831)
  - Ref: [LightRAG, arXiv:2410.05779](https://arxiv.org/abs/2410.05779)
- **Contextual Retrieval + Hybrid Search + Query Rewriting + Reranking** — Prepend chunk-specific context before indexing, combine lexical and dense retrieval, rewrite queries, and rerank for precision.
  - Ref: [Anthropic, Contextual Retrieval](https://www.anthropic.com/news/contextual-retrieval)
  - Ref: [IRCoT, arXiv:2212.10509](https://arxiv.org/abs/2212.10509)
  - Ref: [Rewrite-Retrieve-Read, arXiv:2305.14283](https://arxiv.org/abs/2305.14283)
  - Ref: [RankGPT, arXiv:2304.09542](https://arxiv.org/abs/2304.09542)
- **Access-Controlled + Provenance + Quarantine + Freshness** — Enforce ACLs at retrieval time, track sources, isolate untrusted content, and expire stale data.
- **Context Engineering + Isolation + Compaction + Working Memory** — Curate what enters the window and summarize or offload the rest.
  - Ref: [MemGPT, arXiv:2310.08560](https://arxiv.org/abs/2310.08560)
  - Ref: [Generative Agents, arXiv:2304.03442](https://arxiv.org/abs/2304.03442)
  - Ref: [Lost in the Middle, arXiv:2307.03172](https://arxiv.org/abs/2307.03172)
- **Episodic, Semantic & Temporal Graph Memory** — Extract, consolidate, and invalidate facts across sessions with temporal validity windows rather than appending raw transcripts.
  - Ref: [Zep (Temporal KG), arXiv:2501.13956](https://arxiv.org/abs/2501.13956)
  - Ref: [Mem0, arXiv:2504.19413](https://arxiv.org/abs/2504.19413)
- **Skill Library** — Store verified, reusable procedures the agent can retrieve and compose.
  - Ref: [Voyager, arXiv:2305.16291](https://arxiv.org/abs/2305.16291)

## P2: Evaluation

- **Golden Tasks + Trace-Based Eval + Deterministic Verification** — Regression-test agents on fixed tasks, scoring trajectories and outcomes across multi-turn tool and environment interactions.
  - Ref: [SWE-bench, arXiv:2310.06770](https://arxiv.org/abs/2310.06770)
  - Ref: [τ-bench, arXiv:2406.12045](https://arxiv.org/abs/2406.12045)
  - Ref: [GAIA, arXiv:2311.12983](https://arxiv.org/abs/2311.12983)
  - Ref: [OSWorld, arXiv:2404.07972](https://arxiv.org/abs/2404.07972)
- **LLM-as-Judge** — Use a model to score outputs where rules can't, and calibrate against humans.
  - Ref: [G-Eval, arXiv:2303.16634](https://arxiv.org/abs/2303.16634)
  - Ref: [MT-Bench / LLM-as-a-Judge, arXiv:2306.05685](https://arxiv.org/abs/2306.05685)
- **Shadow / Canary + Versioning** — Roll out prompt, model, and tool changes gradually with fast rollback.

## P3: Multi-Agent (ONLY IF SCORECARD JUSTIFIES)

- **Supervisor + Delegation with Typed Contracts** — Specialists receive typed tasks and return typed, verifiable results.
  - Ref: [Magentic-One, arXiv:2411.04468](https://arxiv.org/abs/2411.04468)
  - Ref: [MetaGPT, arXiv:2308.00352](https://arxiv.org/abs/2308.00352)
- **Scoped Handoffs (Routine + Context Transfer)** — Transfer active control to a specialist agent with a pruned, schema-validated state payload rather than nested middleman calls.
  - Ref: [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/)
- **Debate** — Multiple agents argue and converge to improve factuality and reasoning.
  - Ref: [arXiv:2305.14325](https://arxiv.org/abs/2305.14325)
  - Ref: [arXiv:2305.19118](https://arxiv.org/abs/2305.19118)
- **Mixture of Agents** — Layered agents refine one another's outputs.
  - Ref: [arXiv:2406.04692](https://arxiv.org/abs/2406.04692)
- **Independent Verification** — A separate agent with separate context checks the result using tools.
  - Ref: [CRITIC, arXiv:2305.11738](https://arxiv.org/abs/2305.11738)
- **Capability Routing + Registry + Budget Control** — Route by declared capability and cap per-agent spend and steps.
- **Know the failure modes** — Study why multi-agent systems fail before adding one.
  - Ref: [Why Do Multi-Agent LLM Systems Fail? (MAST), arXiv:2503.13657](https://arxiv.org/abs/2503.13657)
- **Avoid:** Peer-to-Peer / Blackboard / Group Collaboration (poor auditability).

## P4: Advanced (ONLY AFTER P0–P3)

- Memory consolidation, adaptive RAG, compute-optimal multi-agent test-time scaling, and MoA only with measured business value.

---

## Appendix: Research Techniques (Not Production Patterns)

- **Chain-of-Thought** — [arXiv:2201.11903](https://arxiv.org/abs/2201.11903)
- **Quiet-STaR** — [arXiv:2403.09629](https://arxiv.org/abs/2403.09629)
- **Buffer of Thoughts** — [arXiv:2406.04271](https://arxiv.org/abs/2406.04271)
- **LATS** — [arXiv:2310.04406](https://arxiv.org/abs/2310.04406)
- **ReWOO** — [arXiv:2305.18323](https://arxiv.org/abs/2305.18323)
- **LLMCompiler** — [arXiv:2312.04511](https://arxiv.org/abs/2312.04511)
- **SayCan** — [arXiv:2204.01691](https://arxiv.org/abs/2204.01691)
- **LLM+P** — [arXiv:2304.11477](https://arxiv.org/abs/2304.11477)

## Appendix: Frameworks & Systems

- **AutoGen** — [arXiv:2308.08155](https://arxiv.org/abs/2308.08155)
- **Magentic-One** — [arXiv:2411.04468](https://arxiv.org/abs/2411.04468)
- **MetaGPT** — [arXiv:2308.00352](https://arxiv.org/abs/2308.00352)
- **Letta / MemGPT** — [arXiv:2310.08560](https://arxiv.org/abs/2310.08560)
- **Zep / Graphiti** — [arXiv:2501.13956](https://arxiv.org/abs/2501.13956)
- **Mem0** — [arXiv:2504.19413](https://arxiv.org/abs/2504.19413)
- **Voyager** — [arXiv:2305.16291](https://arxiv.org/abs/2305.16291)
- **Generative Agents** — [arXiv:2304.03442](https://arxiv.org/abs/2304.03442)
- **SWE-agent** — [arXiv:2405.15793](https://arxiv.org/abs/2405.15793)
- **OpenAI Agents SDK** — [docs](https://openai.github.io/openai-agents-python/)
- **Anthropic Agent SDK** — [docs](https://docs.anthropic.com/en/docs/agents-and-tools/claude-agent-sdk)
- **Google ADK (Agent Development Kit)** — [docs](https://google.github.io/adk-docs/)
- **CrewAI** — [docs](https://docs.crewai.com/) (no arXiv)
- **LangGraph** — [docs](https://langchain-ai.github.io/langgraph/)

