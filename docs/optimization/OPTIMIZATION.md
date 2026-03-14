# Skill Optimization Results

Automated optimization of master-plan skill files using Optuna Bayesian search
with LLM-as-judge evaluation (gemini-2.5-flash-lite via OpenRouter).

## Method
1. Parse each SKILL.md into sections
2. Parameterize: section toggles (on/off), condensed mode (remove examples), bold rules
3. Optuna TPE samples 20 variants per skill
4. Each variant scored against benchmark tasks by LLM judge (0-10 scale)
5. Best variant replaces original

## Results

| Skill | Baseline | Optimized | Delta | Action |
|-------|----------|-----------|-------|--------|
| **next** | 4.75/10 | **7.50/10** | **+58%** | **Replaced** |
| **done** | 6.33/10 | **7.33/10** | **+16%** | **Replaced** |
| save | 7.00/10 | 5.00/10 | -29% | Kept original |
| task | 8.00/10 | 7.33/10 | -8% | Kept original |

## Key Findings

### `next` skill
- **Condensed + bold rules** dramatically improved task prioritization accuracy
- Removed: Triggers, Arguments, Workflow, MASTER_PLAN update rules sections
- Kept: only the core Rules section (5 rules)
- The model performs better with concise directives than detailed workflows

### `done` skill
- Kept full workflow (test → commit → push → update) — removing steps caused failures
- Removed: Triggers section, Files-to-never-commit section
- The important rules about test verification were critical to keep

### `save` and `task`
- Already well-optimized — any section removal degraded performance
- These skills have high information density with no removable fluff

## Benchmark Tasks
See `*_optimization.json` files for full trial data and per-task scores.

## Reproduction
```bash
python3 modules/skill-optimizer/optimizer.py skills/next/SKILL.md --trials 20 --model cheap
```
