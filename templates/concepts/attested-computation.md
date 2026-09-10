---
# 完整档：仅用于"数字必须由经批准代码复算、禁止 Agent 自行估算"的强一致场景（如财务核心指标）。
# 一般项目/课程知识库用 standard.md，不要硬套本档。字段权威定义见 SPEC §10。
type: Attested Computation
title: "{指标名，如：年度营业收入}"
description: "{该指标按某口径计算的方式，一句话}"
status: stable
runtime: python                     # 必填：bigquery/postgres/dbt/python/Looker…，决定 parameters 语义
parameters:                         # Agent 只能给这些"空洞"填值，禁止改 computation 本体
  - { name: year, type: integer, required: true }
# computation: references/computations/xxx.py   # 计算本体较长时用路径；缺省则用正文 # Computation 代码块
executor:
  resource: references/skills/run.md          # 怎么运行的说明或代码
  receipt: [run_id, executed_code, result]    # 一次运行必须回传的凭据字段
attester:
  resource: references/attesters/xxx.py      # 无 LLM 的确定性校验代码：拿 receipt 出结论
generated: { by: "doubao/okf-wiki", at: 2026-09-07T15:30:00+08:00 }
verified: { by: "human:{审核人id}", at: 2026-09-07T20:00:00+08:00 }
stale_after: 2026-12-31T23:59:59+08:00
sources:
  - id: policy-1
    resource: "{口径/政策来源 URL 或路径}"
    title: "{口径文档标题}"
---

# {指标名}

## Definition
{指标口径的文字定义，链接到使用它的上层概念。}[^policy-1]

# Computation

```python
# 经批准的计算本体（或用 computation 字段指向外部文件）。
# Agent 只能绑定 parameters 的值，不得编写/修改此处逻辑。
```

[^policy-1]: {口径文档标题}
