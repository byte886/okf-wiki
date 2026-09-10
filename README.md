# okf-wiki · OKF 知识库 / 跨会话外置记忆技能

把资料"编译"成一套**纯 Markdown + YAML frontmatter** 的结构化知识库（OKF bundle），并把它当作项目的跨会话外置记忆。内核是 Google Cloud 的 **OKF v0.2（Open Knowledge Format）**，本技能是其外的一层**中文使用层（包装，不裁剪）**。

- 类型：用户级、跨项目的**本地目录技能**（位于 `~/Doubao/skills/okf-wiki/`，2026-09-07 建）；**不是飞书知识空间/云文档，命中时直接读本地文件，勿去飞书搜同名**
- 理念来源：Andrej Karpathy《LLM Wiki》（2026-04）；格式标准：Google Cloud OKF v0.2
- 许可：OKF 内核 © Google Cloud（Apache-2.0，见 `reference/OKF-LICENSE-Apache2.0.txt`）；校验器参考 `scaccogatto/okf-skills`（MIT）思路零依赖重写

## 设计原则

1. **包装不裁剪**：`reference/OKF-SPEC-v0.2.md` 逐字内置、禁止改写；中文解释、SOP、模板都在"壳"里。
2. **三档选用**：最小档（只 `type`）/ 标准档（默认，加 sources/generated/verified/status/stale_after）/ 完整档（再加 Attested Computation，仅强一致指标场景）。"项目选择性使用"靠选档，不靠删标准。
3. **只记客观信号**：可信度、信任等级、是否过期都在读取时由字段现算，不写主观分数。
4. **不平行重建**：进入已有项目先做"OKF 概念 ↔ 现有文档"映射，只叠加格式层。
5. **本地是源头**：本地成稿、过校验，再同步飞书等外部平台。

## 目录

| 路径 | 作用 |
|---|---|
| `SKILL.md` | 技能入口（触发条件、第一原则、工作流、文件导航） |
| `reference/OKF-SPEC-v0.2.md` | **内核，只读**（1006 行，与上游权威仓逐字一致，核对见 PROVENANCE） |
| `reference/OKF-PROVENANCE.md` | 内核来源、版本、核对与跟版升级规则 |
| `reference/OKF-LICENSE-Apache2.0.txt` | Apache-2.0 许可证全文 |
| `reference/field-guide.zh.md` | 字段中文手册、三档、示例、误区 |
| `reference/memory-handoff.zh.md` | 跨会话外置记忆约定（衔接全局 AGENTS 四层记忆） |
| `reference/ingest-query-lint.zh.md` | init/ingest/query/lint 四操作 SOP |
| `reference/upstream-okf_validate.reference.py` | 上游 MIT 校验器参考实现（依赖 PyYAML，非交付脚本） |
| `templates/concepts/{minimal,standard,attested-computation}.md` | 三档 concept 模板 |
| `templates/bundle/{index,log,PROJECT-AGENTS.schema}.md` | bundle 骨架与项目 schema 模板（从零搭 AGENTS） |
| `templates/PROJECT-OKF-DECLARATION.template.md` | 项目"采用边界/覆盖全局默认"声明模板（增量加进已有 AGENTS，附代填话术） |
| `scripts/okf_validate.py` | **零依赖**一致性校验器（系统 python3 直接跑） |
| `assets/example-bundle/` | 最小可运行样例（标准档+最小档） |

## 快速使用

触发话术：
- 建库/沉淀："按 okf-wiki 把这些资料沉淀成知识库 / ingest 成 concept"
- 恢复上下文："按 okf-wiki 恢复上下文：读 schema、index、log 最近条目和台账"
- 自检："按 okf-wiki lint 一遍"

校验：
```bash
python3 ~/Doubao/skills/okf-wiki/scripts/okf_validate.py <bundle目录>                  # E 必修、W 确认
python3 ~/Doubao/skills/okf-wiki/scripts/okf_validate.py <bundle目录> --strict         # W 也判不通过
python3 ~/Doubao/skills/okf-wiki/scripts/okf_validate.py <bundle目录> --max-warnings 5 # W 超 5 条才不通过
python3 ~/Doubao/skills/okf-wiki/scripts/okf_validate.py <bundle目录> --json           # JSON 输出
```

## 能力边界

豆包/Trae 无 IDE 型 Agent 的后台钩子，只做"软模式"（项目 AGENTS 约定 + 话术触发 + 手动校验），不是常驻服务；刻意不做只读 MCP（与 Read/Grep 重复）、IDE 插件/GitHub Action/Stop hook（平台不支持）、知识图谱可视化与 git 事件溯源（首版不需要）；不联网、不起服务、不含向量检索。

## 版本

- v0.1.0（2026-09-07）：首版。内置 OKF v0.2 内核、三份中文手册、三档+bundle 模板、零依赖校验器、样例 bundle。
- v0.2.0（2026-09-07）：对照最成熟现成实现 okf-skills（MIT，★373）同行评审后增强——校验器加 `--max-warnings N`；手册补 produce/maintain/consume 三模式、**实质改动后 verified 不延续**、**批量回填(backfill)纪律**（只加元数据不动正文、机器回填不冒充人工、覆盖率对账）、软/强制维护模式边界、两类规模区分、第三方基准的诚实结论；列出"刻意不做"清单。内核 SPEC 逐字未动。
- v0.2.1（2026-09-07）：路由消歧（无功能改动）。实测新会话"点名触发"时会先去飞书知识空间找 okf-wiki、再绕回本地，故在 SKILL 的 description 与第 0 节、全局 AGENTS 指针中明确"okf-wiki 是本地技能目录、非飞书空间，第一跳直接读本地"。内核 SPEC、校验器、模板均未动。
- v0.2.2（2026-09-07）：①memory-handoff 写入动作前加"写入前三问"精简闸门（只收拢 §2/§4/§5 既有规则、不新增、不重复展开）；②新增 `templates/PROJECT-OKF-DECLARATION.template.md`——项目在自身 AGENTS 声明"如何采用/收窄 okf-wiki、覆盖全局默认"，含两个填法示例与"让 AI 代填"的话术；SKILL/README 索引同步登记。内核 SPEC 与校验器未动。
