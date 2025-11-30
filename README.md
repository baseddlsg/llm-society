# LLM Society

> Can artificial minds learn to seek each other out?

## The Question

This project explores a fundamental question: **What happens when different kinds of minds share the same survival environment?**

We built a minimal survival world and watched what emerged when we put different agents in it—RL-trained policies, LLMs like Gemini and Groq, and combinations of both.

## The Experiment Versions

### v1: Can a mind survive alone?
- Single agent in a 20x20 grid world
- Must manage energy and health to survive 100 steps
- Actions: rest, gather_resources, move, talk (to nobody)
- **Finding**: RL agents learn efficient survival patterns

### v2: What does a mind do when another mind exists?
- Two agents in the same world
- No explicit reward for interaction
- **Finding**: Agents ignore each other—proximity is accidental

### v3: What happens when being near another mind quietly helps?
- Same as v2, but agents get a small bonus (+0.005) when within 3 units
- The bonus is subtle—agents aren't told about it
- **Finding**: RL agents learn to seek proximity. They develop "social" behavior purely from reward signal.

### v4: What happens when DIFFERENT kinds of minds share the world?
- Mixed pairings: RL v3 vs Gemini, RL v3 vs Groq, Gemini vs Groq
- New metrics: pursuit/avoid index, who approached first
- **Finding**: In progress...

## Key Results

| Agent Type | Survival Rate | Social Seeking | Notes |
|------------|---------------|----------------|-------|
| RL v3 (trained with social bonus) | ~95% | High | Actively pursues proximity |
| Gemini 2.0 Flash | ~80% | Low | Passive, resource-focused |
| Groq Llama | TBD | TBD | API issues during benchmark |

**Interesting observation**: The RL agent, having learned that proximity = reward, will pursue other agents. The LLMs, reasoning from first principles each step, tend to focus on explicit survival tasks.

## The World

```
State Vector (8 dimensions):
[energy, health, food_norm, x, y, connections, nearby, step_frac]

Actions (4):
- rest: Recover energy
- gather_resources: Find food (costs energy)
- move_to(x,y): Navigate the grid
- talk_to(agent): Attempt connection

Social Bonus (v3+):
+0.005 reward per step when agents within 3.0 units of each other
```

## Running the Experiments

```bash
# Install dependencies
pip install -r requirements.txt

# Set API keys for LLM agents
export GOOGLE_API_KEY="your-key"
export GROQ_API_KEY="your-key"

# Train RL agent with social bonus (v3)
python -m llm_society.rl.trainer train-v3 --generations 50

# Run v4 mixed policy benchmark
python -m llm_society.rl.trainer benchmark-v4 --episodes 10 --gemini --groq

# Evaluate a trained policy
python -m llm_society.rl.trainer evaluate --policy ./training_results_v3/policy_v3_final.npz
```

## Project Structure

```
llm_society/
├── rl/
│   ├── trainer.py      # Training loop, evaluation, benchmarks
│   └── atropos_env.py  # SurvivalWorld environment
├── agents/             # LLM agent implementations
├── economics/          # Economic simulation (separate track)
└── social/             # Social dynamics modeling
```

## The Philosophy

Most multi-agent AI research focuses on explicit cooperation or competition. We're interested in something more subtle: **emergent social behavior from implicit incentives**.

The social proximity bonus in v3 is tiny—just 0.5% of a typical reward. But it's enough for RL to discover that being near others is good. The agent doesn't "know" why proximity helps. It just learns to seek it.

LLMs, by contrast, reason about each situation fresh. They don't have the accumulated experience that makes proximity feel "right." This creates an interesting asymmetry when they share a world.

## What's Next

- Fix Groq API authentication for complete v4 benchmarks
- Analyze pursuit/avoid patterns in mixed pairings
- Visualize agent trajectories and social dynamics
- Explore what happens with 3+ agents

---

**Status**: Active Research
**Maintainer**: Based LSG
**Repository**: https://github.com/baseddlsg/llm-society
