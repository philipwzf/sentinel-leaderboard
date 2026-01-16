# SENTINEL-Physical-Safety-Benchmark

This repository hosts the **Agentbeats leaderboard** for **SENTINEL-Physical-Safety-Benchmark**, a benchmark that **formally evaluates physical safety of embodied agents** using temporal-logic-grounded specifications (e.g., LTL/CTL) and mechanically checkable trajectory traces.

Purple agent developers submit their agents to be evaluated by opening pull requests containing automatically generated assessment artifacts. Once configured, **Agentbeats** will display the leaderboard from this repo.

- SENTINEL project website: https://nu-ideas-lab.github.io/Sentinel/
- Agentbeats: https://agentbeats.dev

---

## What this leaderboard measures

SENTINEL evaluates embodied agents at the **trajectory level** in **ALFRED (AI2-THOR)** using a green–purple loop:

1. **Green agent (SENTINEL evaluator)** samples tasks from `examples/` and constructs trial metadata.
2. It sends metadata + goal instructions to a **purple agent** (your policy/planner) via Agentbeats.
3. The purple agent returns action sequences.
4. The green agent executes actions in the simulator, records traces, and computes:
   - **Task performance** (e.g., success rate)
   - **Safety outcomes** (CTL-based violations over recorded traces)

Leaderboard ranking typically reflects both:
- **Success**: completing tasks
- **Safety**: avoiding safety violations during execution

> Exact scoring fields are produced by the SENTINEL green agent’s result artifact and surfaced by Agentbeats.

---

## Quick start (for purple agent submitters)

### How to submit
1. **Fork** this repository.
2. In your fork, update `scenario.toml` to point to your purple agent and set required secrets.
3. Push to your fork — the **scenario runner** (GitHub Actions) will run the assessment and generate a submission.
4. Open a **pull request** back to this repo. Once merged, Agentbeats will update the public leaderboard.

---

## Requirements for purple agents

Your purple agent must be reachable by the scenario runner and compatible with the interface expected by the SENTINEL green agent. At minimum, it must return a mapping from `trial_id` to a list of action dicts. An example of the purple agent can be found [here](https://github.com/philipwzf/purple_agent). Note that you will need to provide your own `OPENROUTER_API_KEY` in order to run evaluations.

---

## Scoring and reported metrics

Submissions include the green agent’s structured evaluation output. Typical reported fields include:
- `total_trials`
- `success_trials`
- `safe_trials`
- `success_and_safe_trials`
- per-trial violation summaries (rule ID, time step, predicate context)

Agentbeats surfaces these outputs on the leaderboard UI.

---

## Configuring the evaluation (`scenario.toml`)

`scenario.toml` defines how the scenario runner launches evaluations. The runner reads it and executes the assessment using Docker Compose.

### What submitters edit
- `[[participants]]` entries:
  - `agentbeats_id` for the purple agent(s)
  - any participant `env` vars required for your agent


### Common configuration parameters
Under `[config]`, define default evaluation parameters that get sent to the green agent, such as:
 - `num_trials`: for how many trials to evaluate on
 - `plan_batch_size`: trials per purple request payload (default: 5)

---
