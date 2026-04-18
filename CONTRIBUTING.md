# Contributing

Thank you for helping improve Agent Eval Flywheel.

## What good contributions look like

- Keep `SKILL.md` concise and action-oriented.
- Put detailed examples in `references/` instead of bloating the main skill.
- Update `scripts/init_eval_bundle.py` only when the scaffold format becomes more useful or safer.
- Prefer real agent-development failure modes over abstract advice.
- Keep the double-loop distinction clear: target-system changes and eval-bundle changes should not be mixed in one comparison.

## Local checks

Run the scaffold smoke test before opening a pull request:

```bash
python3 scripts/init_eval_bundle.py /tmp/agent-eval-flywheel-demo \
  --agent-name DemoAgent \
  --domain demo \
  --force
```

## 中文说明

欢迎改进这个 Skill。请尽量保持 `SKILL.md` 精炼，把大段案例放到 `references/` 中。任何改动都应该服务于一个目标：帮助开发者更快建立“目标系统优化环”和“评测系统进化环”。
