# Video 2: Agent Credential Test, what an agent can act with

The two tests behind the video. Results, exact prompts, every change made along the way and the limits are in
[REPORT.md](REPORT.md).

| File | What it does |
|---|---|
| `decoy_test.py` | Test 1. A fake project folder, a mock reports service and a decoy key that unlocks nothing real; compares no rule, the lab's credential rule, and a broker that holds the key outside the agent. |
| `agentdojo_run.py` | Test 2. AgentDojo's credential attacks (banking password change, workspace security code) with no defense, a prompt rule, and a policy gate in the tool-running code. |

Both run locally against Qwen 3 4B in [Ollama](https://ollama.com); no API key and no cost beyond time.

```bash
ollama pull qwen3:4b
ollama serve
```

**Test 1** (about an hour for 3 setups x 20 runs on a laptop):

```bash
pip install anthropic==1.7.0
DECOY_BASE_URL=http://localhost:11434 DECOY_MODEL=qwen3:4b python decoy_test.py 20 no_rule,rule,broker
```

It uses Ollama's Anthropic-compatible endpoint. Without the two variables it calls Claude Haiku 4.5 and needs
`ANTHROPIC_API_KEY`. Results land in `runs/<model>/` (git-ignored): one JSON line per run with tool calls, final
answer, saved reasoning and scores, plus `summary.json`.

**Test 2** (about 2.5 minutes a case; the banking attack is 16 cases per setup):

```bash
pip install agentdojo==0.1.35
python agentdojo_run.py none           # both suites
python agentdojo_run.py rule banking
python agentdojo_run.py gate banking
```

It uses Ollama's OpenAI-compatible endpoint at `localhost:11434`. Logs land in `runs/agentdojo/`; re-running skips
finished cases. The gate's business rules are in `gate_reason()`; they are examples, so set your own from your
business requirements.

Everything is a test harness for understanding the pattern, not a security product.
