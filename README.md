# Agent Eval Flywheel

<p align="center">
  <img src="./assets/hero.png" alt="Agent Eval Flywheel hero image" width="100%">
</p>

<p align="center">
  <strong>Benchmark-driven double-loop development for AI agents.</strong><br>
  <strong>面向 AI Agent 的评测驱动双环开发 Skill。</strong>
</p>

<p align="center">
  <a href="#english">English</a> |
  <a href="#中文">中文</a>
</p>

---

## English

Agent Eval Flywheel is a Codex skill for building AI agents with evaluations from day one. It helps you turn a vague product idea into goals, boundaries, acceptance gates, scoring rubrics, seed evals, regression suites, and a double-loop improvement process where the target system and the eval bundle evolve separately.

The core idea is simple: do not wait until the agent is "done" to evaluate it. Define the job, freeze a small eval surface, run a baseline, improve the agent, and continuously feed failures back into the eval suite.

### What it gives you

- A repeatable workflow for eval-driven agent development.
- A distinction between hard acceptance gates and soft quality scores.
- A seed eval design pattern for real tasks, edge cases, adversarial cases, and virtual users.
- A target-system loop for improving the agent.
- An eval-bundle loop for improving the benchmark itself.
- A starter scaffold script for eval artifacts.

### When to use it

Use this skill when you are:

- starting a new AI agent project
- adding evals to an existing agent
- defining product-quality benchmarks
- turning user failures into regression cases
- designing virtual-user or simulator-based evals
- deciding whether a model, prompt, tool, retrieval, or architecture change actually helped

### Install

Clone the repository:

```bash
git clone https://github.com/Larkspur-Wang/agent-eval-flywheel-skill.git
cd agent-eval-flywheel-skill
```

Install as a local Codex skill by symlinking it into your Codex skills directory:

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
ln -sfn "$PWD" "${CODEX_HOME:-$HOME/.codex}/skills/agent-eval-flywheel"
```

Then invoke it in Codex:

```text
Use $agent-eval-flywheel to define the goal, boundaries, rubric, seed evals, and double-loop iteration plan for my support agent.
```

### Scaffold an eval bundle

The skill includes a small helper for creating a starter eval bundle:

```bash
python3 scripts/init_eval_bundle.py ./evals/v0 \
  --agent-name "Support Agent" \
  --domain "customer-support"
```

It creates:

```text
charter.md
rubric.yaml
seed-cases.yaml
regression-cases.yaml
error-taxonomy.md
results.tsv
```

### Repository layout

```text
SKILL.md                         # Main Codex skill instructions
agents/openai.yaml               # Skill UI metadata
references/                      # Detailed references loaded only when needed
scripts/init_eval_bundle.py       # Eval bundle scaffold helper
.github/                         # Issue templates and PR template
```

### CI template

The repository includes a ready-to-enable GitHub Actions validator at:

```text
references/github-actions/validate.yml.example
```

If you want live CI in your own copy, move it to:

```text
.github/workflows/validate.yml
```

### Influences

This skill is inspired by:

- [karpathy/autoresearch](https://github.com/karpathy/autoresearch), especially the idea of a fixed evaluation harness, narrow mutable surface, baseline-first iteration, and a run ledger.
- [OpenAI eval guidance](https://openai.com/index/evals-drive-next-chapter-of-ai/) and [agent evals documentation](https://developers.openai.com/api/docs/guides/agent-evals).
- [Anthropic's Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents), especially the preference for simple, composable agent patterns.

It adapts those ideas for product and agent development, where humans remain the trigger for goal changes, permission expansion, metric conflicts, and production rollout.

### Validate locally

```bash
python3 scripts/init_eval_bundle.py /tmp/agent-eval-flywheel-demo \
  --agent-name DemoAgent \
  --domain demo \
  --force
```

### License

MIT

---

## 中文

Agent Eval Flywheel 是一个 Codex Skill，用来指导 AI Agent 项目从第一天开始建立评测驱动的开发闭环。它帮助你把一个模糊的产品想法，拆成目标、边界、验收门槛、评分 Rubric、种子评测集、回归集，以及“产品系统”和“评测系统”分开演化的双环流程。

核心思想很简单：不要等 Agent 做完以后再评测。先定义任务，冻结一小块评价面，跑 baseline，再改 Agent，然后把失败案例持续回灌进评测集。

### 它能帮你做什么

- 建立一套可重复的 Agent 评测驱动开发流程。
- 区分硬验收门槛和软质量评分。
- 设计覆盖真实任务、边界场景、对抗样本和虚拟用户的种子评测集。
- 运行目标系统优化环，用 benchmark 指导 Agent 改进。
- 运行评测系统进化环，用真实失败案例校准 benchmark。
- 用脚手架快速生成评测资产。

### 什么时候使用

适合这些场景：

- 启动一个新的 AI Agent 项目
- 给已有 Agent 补评测体系
- 定义产品体验 benchmark
- 把用户失败案例沉淀成 regression case
- 设计虚拟用户或用户模拟器评测
- 判断一次模型、prompt、工具、召回或架构改动是否真的变好

### 安装

克隆仓库：

```bash
git clone https://github.com/Larkspur-Wang/agent-eval-flywheel-skill.git
cd agent-eval-flywheel-skill
```

把它软链接到本地 Codex skills 目录：

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
ln -sfn "$PWD" "${CODEX_HOME:-$HOME/.codex}/skills/agent-eval-flywheel"
```

然后在 Codex 中这样调用：

```text
Use $agent-eval-flywheel to define the goal, boundaries, rubric, seed evals, and double-loop iteration plan for my support agent.
```

### 快速生成评测包

```bash
python3 scripts/init_eval_bundle.py ./evals/v0 \
  --agent-name "Support Agent" \
  --domain "customer-support"
```

会生成：

```text
charter.md
rubric.yaml
seed-cases.yaml
regression-cases.yaml
error-taxonomy.md
results.tsv
```

### 设计来源

这个 skill 参考了：

- [karpathy/autoresearch](https://github.com/karpathy/autoresearch) 的固定评价面、窄修改面、baseline-first、run ledger 思想。
- [OpenAI eval 文章](https://openai.com/index/evals-drive-next-chapter-of-ai/) 和 [agent evals 文档](https://developers.openai.com/api/docs/guides/agent-evals)。
- [Anthropic Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents) 中关于简单、可组合 agent pattern 的思路。

它没有照搬 `autoresearch` 的“单指标无限自治”，而是把它改造成更适合产品与 Agent 系统的版本：人类保留目标变化、权限扩张、指标冲突和上线发布的关键 trigger。

### 本地验证

```bash
python3 scripts/init_eval_bundle.py /tmp/agent-eval-flywheel-demo \
  --agent-name DemoAgent \
  --domain demo \
  --force
```

### CI 模板

仓库里已经附带一个可直接启用的 GitHub Actions 校验模板：

```text
references/github-actions/validate.yml.example
```

如果你希望在自己的仓库里开启 CI，把它移动到：

```text
.github/workflows/validate.yml
```

### License

MIT
