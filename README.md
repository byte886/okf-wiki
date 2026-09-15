# okf-wiki · OKF 知识库 / 跨会话外置记忆技能

把资料"编译"成一套**纯 Markdown + YAML frontmatter** 的结构化知识库（OKF bundle），并把它当作项目的跨会话外置记忆。内核是 Google Cloud 的 **OKF v0.2（Open Knowledge Format）**，本技能是其外的一层**中文使用层（包装，不裁剪）**。

- 类型：用户级、跨项目的**本地目录技能**（位于 `~/Doubao/skills/okf-wiki/`）；**不是飞书知识空间/云文档，命中时直接读本地文件，勿去飞书搜同名**
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
| `reference/field-guide.zh.md` | 字段中文手册、最小骨架、字段逐项、示例 |
| `reference/bundle-files-and-pitfalls.zh.md` | 三档选用、index/log 写法、链接规则、常见误区 |
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
