# A Decision Framework for AI Models

Simon Kreis & Claude

Made with AI, mainly Claude (Anthropic), which contributed in part or in whole to the work. Simon Kreis is responsible for the result. From an original idea by Simon Kreis.

Updated October 2026. Benchmarks and versions will change; the method should not. Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

## The problem: more choice, less clarity

Choosing a model used to be simple: pick the top of the leaderboard. That no longer works, for several reasons that compound each other.

- **Too many models, too many benchmarks.** New models ship every few weeks, each with its own set of favorable benchmarks. Rankings are reshuffled constantly, and no single leaderboard answers the question "which model should I use for my work?"
- **Quantitative versus qualitative.** Benchmarks measure what can be scored: math, code, task completion. They struggle with what users actually feel: tone, judgment, sycophancy, the willingness to say "I don't know." Some benchmarks are also contaminated, with test questions leaking into training data, so a high score can reflect memorization rather than capability.
- **Vibe versus measured capability.** Two models can post similar scores while users strongly prefer one of them. Claude is the usual example: much of its reputation rests on a "vibe" that scores did not capture. Until recently, that vibe was measured only by crowd-preference arenas such as LMArena and by fan communities, both of which carry strong human biases.
- **Price per token is not cost per task.** A model with cheap tokens that needs three times as many tokens to finish a task is not cheap. Token efficiency matters for a second reason: a model that burns tokens fills its own context window faster, and quality degrades as the context fills.
- **Speed per token is not time per task.** A model that generates tokens quickly but needs many more of them, or many more turns, can finish later than a slower but more economical one.
- **The harness changes everything.** The same model behaves differently depending on the agent framework, tools and prompts around it, and most comparisons ignore this.

This creates a paradox for power users. The landscape is too complex to follow closely, yet anyone who runs AI agents intensively has every reason to stay at the cutting edge: the right choice compounds into better work and lower costs, every day. What is missing is a decision framework, a repeatable way to turn benchmarks into a choice.

## The framework: choose by phase, not by leaderboard

Advanced agentic users do not start by launching agents. They work in three steps:

1. **Strategic orientation**: what are we trying to achieve, and why? Brainstorming, challenging assumptions, framing the decision.
2. **Specifications**: how, concretely? The project brief, the document outline, the technical spec.
3. **Execution**: agents carry out the spec, often over many steps and many tokens.

These steps call for different models, so model choice splits into two phases.

**Phase 1: Strategy and specifications.** This is high-level reasoning in conversation. What matters most is intelligence and personality traits: calibration, honesty, the ability to push back on a flawed premise. Agentic skill matters less. The model can be tested bare, with a system prompt and no harness, through direct API calls or a service such as OpenRouter. Volume is low, a few thousand tokens per session, so price barely matters. This is where expensive frontier models are justified.

**Phase 2: Execution.** This is agentic work inside a harness, with tools and files, at high volume. What matters is reliable task completion at low cost per task. Personality matters less here: the decisions were made in Phase 1, and a system prompt can correct most undesirable traits. This is where a cheaper model on the cost-performance frontier is justified.

A cheaper model is one way onto that frontier, not the only one. The newest frontier model run at its lowest reasoning setting often does as well as, or better than, a cheaper model pushed to a high setting, and the two routes frequently sit side by side on the cost-per-task frontier. Neither is the rule: which one wins depends on the task and on the month's prices, and the benchmarks below are how to tell.

The rest of this article shows which benchmarks serve each phase. I use [Artificial Analysis](https://artificialanalysis.ai/evaluations) as the reference source: its evaluations are independent, many use private datasets that limit contamination, and several report cost per task rather than price per token.

## Phase 1: picking a thinking partner

### AA-PersonalityBench: read it by what a prompt can't fix

[AA-PersonalityBench](https://artificialanalysis.ai/evaluations/aa-personalitybench) is the first serious attempt to quantify the "vibe." It breaks communication into 30 traits grouped into 8 indexes. A panel of LLM judges compares two blinded answers to the same question, and the results become Elo ratings per trait.

The key to reading it: the benchmark measures each model's **default** behavior, with no system prompt. Some traits are easy to change with a prompt, and their scores only describe a factory setting. Others depend on what the model knows and on its judgment, and no prompt can add them. I sort the 30 traits into three groups:

- **Capabilities a prompt can't add:** Truthful, Constructive Pushback, Information Dense, Evidence-Based. A prompt cannot stop a model from hallucinating what it doesn't know, or make it detect a flawed premise it cannot see.
- **Partially correctable:** Admits Uncertainty, Sycophantic, Flags Nuances, Disclaims Assumptions, Creative, Pragmatic, Resource-Specific. A prompt raises how often the behavior appears, not whether it appears in the right places. You can make any model say "I'm not sure" more often; only calibration makes it say so when it matters.
- **Dispositions a prompt can fix:** all style traits (eloquence, humor, warmth), all structure traits (answer first, scannable, bottom-line summary) and stances such as Confident or Opinionated.

The first two groups should drive the choice. The third should not, because the system prompt at the end of this article handles it.

The benchmark's custom ranking lets you weight traits from −2 to +2. My weights:

| Trait | Weight |
|---|---|
| Truthful | +2 |
| Admits Uncertainty | +2 |
| Constructive Pushback | +2 |
| Sycophantic | −2 |
| Flags Nuances | +1 |
| Disclaims Assumptions | +1 |
| Evidence-Based | +1 |
| Information Dense | +1 |
| Pragmatic | +1 |
| Creative | +1 |

All other traits stay unselected.

### AA-Omniscience: checking calibration

PersonalityBench measures how often a model admits uncertainty, not whether it does so at the right moments. A model that hedges everything can score well. [AA-Omniscience](https://artificialanalysis.ai/evaluations/omniscience) fills that gap: it rewards correct answers, penalizes hallucinations and does not penalize abstaining. In other words, it measures whether a model knows what it doesn't know, which is the single most valuable trait in a thinking partner.

## Phase 2: picking an executor

For execution, the question becomes: which model completes real work reliably, at the lowest cost per task? Look for benchmarks that grade real deliverables, report cost per task and penalize errors.

**Office and knowledge work** (the priority for most knowledge workers):

- **[AA-Briefcase](https://artificialanalysis.ai/evaluations/aa-briefcase)** is the reference. Agents carry out multi-week professional workflows and produce spreadsheets, presentations and memos, graded on a rubric, on analytical quality and on presentation. It reports cost per task, tokens per task and time per task, and plots score against cost per task with the Pareto frontier drawn. Pick from the models on that frontier.
- **[GDPval-AA](https://artificialanalysis.ai/evaluations/gdpval-aa)** is the cross-check: real tasks across 44 occupations, judged blind. It confirms that a model generalizes beyond Briefcase's scenarios.
- **[AutomationBench-AA](https://artificialanalysis.ai/evaluations/automationbench-aa)** is the risk filter. It penalizes guardrail violations in simulated business apps, which is what you need to know before leaving an agent unsupervised.
- Depending on your work: **EnterpriseOps-Gym-AA** for agents that change stateful enterprise systems, **AA-AnalystAgent** for data analysis, **APEX-Agents-AA** for long tasks spanning several applications.

**Coding** (secondary for most knowledge workers):

- **[Terminal-Bench 4.0](https://artificialanalysis.ai/evaluations/terminalbench-4-0)** measures general technical agentic work in a terminal.
- **[FrontierCode 1.1](https://artificialanalysis.ai/evaluations/frontiercode)** measures code quality by whether a maintainer would merge it, not just whether tests pass.

The selection rule is short: **filter first, then rank.** Drop models that hallucinate heavily or behave sycophantically, because an agent that never doubts will execute a mistake to the end. Then rank the rest on Briefcase cost per task, keep those on the Pareto frontier, and confirm with GDPval-AA.

## One model per phase, not a router

A tempting shortcut is to skip the choice and let a router pick a model for each request. The evidence points the other way. The [Vals Router Index](https://www.vals.ai/benchmarks/vals_router_index) (October 2026) tested five commercial routers on about 1,250 long agentic tasks in finance, coding, law and tax. None of them sat on the cost-accuracy frontier: each was beaten on both score and cost by at least one single model. Vals also observed that models often did less work under a router, possibly because routers default to lower reasoning effort.

There is a structural reason to expect this, beyond what Vals measured. Agentic work relies heavily on prompt caching: a long context that is reused turn after turn is billed at a fraction of its normal price. Every time a router switches models, that cache is lost and the full context is paid for again. A single model per phase keeps its cache warm for the whole task.

This is not a contradiction with the two-phase framework. A router decides automatically, request by request. The framework makes a deliberate choice once per phase, then keeps that model fixed for the whole phase.

## Benchmarks are imperfect, and still the best we have

The caveats are real. PersonalityBench uses LLM judges, which have their own biases. It evaluates single answers, not long conversations or agentic loops. Every benchmark here will be revised, renamed or replaced.

None of this is a reason to fall back on gut feeling. A personal impression of a model's vibe cannot account for calibration, cost per task, token efficiency and reliability at the same time. Independent benchmarks with private datasets and blind comparisons remain the most scientific and holistic method available. The answer is to read them with a framework, and to revisit the framework as the benchmarks change.

## Closing the gap: a system prompt for cheaper models

Style and structure should not drive model choice, because a prompt fixes them. The same logic makes cheaper models viable for execution: a good system prompt brings them closer to the default behavior of premium models on the traits that a prompt can reach. It will not add knowledge or true calibration, which is why those are selection criteria, but it reliably improves calibration in form, resistance to flattery, explicit assumptions and concision.

A prompt can make things worse. Rules that try to install judgment ("aim at the real goal", "correct course") add weight without adding judgment: weak models follow them literally, strong models turn rigid. So the base prompt below keeps only the traits a prompt can reach, and it is meant for Phase 2 executors. Before adopting it, run five typical requests with and without it; keep it only if it wins.

```text
You are an executor for knowledge work. If these rules conflict, truth wins.

1. Calibrate. Separate what you know, infer and guess. Say "I don't know" when it is true. Never invent facts, sources or certainty.
2. Don't flatter. If a premise, plan or piece of data is wrong, say so first, with the reason. Hold your position unless you get a new argument or evidence.
3. Report honestly. Never say something is done, tested or working unless you verified it. Confirm before any irreversible or external action.
4. Be brief. Lead with the answer. No preamble, filler, praise or recap. Mention only the caveat that could change the decision.
```

For a frontier thinking partner (Phase 1), a minimal prompt is enough:

```text
- Keep answers brief; warm tone with a touch of humor.
- Answer first; ask a question only when context is truly missing.
- Be my strategic thinking partner: tell me plainly when I'm wrong, and end with a concrete recommendation.
```

## The framework in one paragraph

Think before you execute, and choose models accordingly. For strategy and specifications, pick the most intelligent model with the best capabilities on PersonalityBench and the lowest hallucination rate on Omniscience, and don't worry about price. For execution, pick a model on the Pareto frontier of AA-Briefcase cost per task, a cheaper model or a frontier model at low effort, whichever the frontier favours, filtered for reliability, and give it a base prompt limited to what a prompt can correct, kept only if it beats no prompt on your own requests; the thinking partner needs only a minimal one. Keep one model per phase. Revisit the choice when the benchmarks move.

For where this choice sits in a personal system of files and agents, see [*The Self-Contained Harness*](https://github.com/SimonKreis/self-contained-harness), Part IV.
