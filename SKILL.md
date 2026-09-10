---
name: okf-wiki
description: 用 OKF（Open Knowledge Format v0.2）在本地项目建立并维护"LLM 编译式知识库 / 跨会话外置记忆"。当用户要把资料沉淀为结构化 Markdown 知识库、给项目建长期记忆或上下文文档、在新会话恢复项目上下文、按统一格式整理知识条目、对知识库做摄入(ingest)/查询(query)/自检(lint)，或提到 OKF / Open Knowledge Format / Karpathy LLM Wiki / 知识包 bundle 时使用。内核是 Google Cloud OKF v0.2（Apache-2.0，原样内置、禁止改写），本技能只提供中文使用层、模板与确定性校验，不绑定任何编辑器或平台。注意：本技能是**本地文件目录技能**（`~/Doubao/skills/okf-wiki`），**不是飞书知识库 / 云文档空间**；被点名或命中时直接读本目录的 SKILL.md 与 reference/，不要先去飞书、云盘搜索同名空间。
compatibility: "方法论/格式本身跨平台；随附脚本与运行环境仅在 macOS(Darwin) 实测，Windows/Linux 未适配。执行前先判平台(uname -s)，涉及脚本/命令时非 macOS 停止并告知需另行适配、不硬跑；将来补齐后按平台分流并分别标注验证状态"
---

# okf-wiki：OKF 知识库 / 跨会话外置记忆

## 平台适用（执行前先读）
- 方法论/格式本身跨平台；但**随附脚本与本套运行环境仅在 macOS（Darwin）实测**。
- 动手前先 `uname -s` 判平台：Darwin 走现有流程；Windows/Linux 只能使用纯方法论部分，一旦涉及脚本/命令，停下提示需另行适配，不硬跑。
- 以后补齐 Windows 后也保留“先判平台 → 按平台分流”的结构，分别标注各平台验证状态。

## 0. 30 秒理解本技能

- **内核**：`reference/OKF-SPEC-v0.2.md`，Google Cloud OKF v0.2 规范逐字内置，**一字不得改写**；有争议以它为准。
- **本技能 = 内核外的一层中文"壳"**：教 AI 何时、如何按 OKF 建/读/维护一个知识包（bundle），以及怎样把 bundle 当作跨会话外置记忆。
- **与 Karpathy LLM Wiki 的关系**：LLM Wiki 是理念（知识"编译一次、持续维护、复利增长"，而非每次从原始资料重新推导）；OKF 是把这个理念落成可移植、跨工具的**格式标准**。本技能用 OKF 落地该理念。
- **形态边界**：豆包 / Trae 没有 Claude Code 那样的后台钩子，**只支持"软模式"**（项目 AGENTS 写约定 + 用户话术触发），做不到保存即自动校验/阻断收尾；不是常驻后台服务。刻意不做只读 MCP（与 Read/Grep 重复）、IDE 插件/GitHub Action/Stop hook（平台不支持）、知识图谱可视化与 git 事件溯源（首版不需要），理由见 `reference/memory-handoff.zh.md` §6。
- **在哪里（避免找错，第一跳就读对）**：本技能就是**本地目录** `~/Doubao/skills/okf-wiki/`，**不是飞书知识空间、不是云文档**。一旦提到 okf-wiki / OKF，直接用本地文件工具读本目录，**不要先去飞书知识库或云盘搜索同名对象**；飞书只是知识成品对外发布的目的地之一，与本技能本身无关。

## 1. 第一原则（强制）

1. **内核不改**：不修改 `OKF-SPEC-v0.2.md`；解释、中文说明、裁剪建议一律写到本技能其它文件。跟版升级 = 整体替换该 SPEC 文件。
2. **包装不裁剪、三档选用**：不删除 OKF 任何字段；"项目按需使用"通过最小/标准/完整三档实现（见 `reference/field-guide.zh.md`），而不是改 SPEC。
3. **只记录客观信号，不存主观分数**：可信度分数、信任等级、是否过期都在**读取时由字段现算**，不写死进文档（SPEC §5）。
4. **不与项目既有文档体系打架**：进入已有项目前，先找它的 README / AGENTS / 文档地图 / 索引 / 台账，能映射就映射、只叠加"格式层"，**绝不平行重建第二套记忆**（见第 6 节）。
5. **本地是源头**：知识成品先在本地成稿并过校验，再同步到飞书等外部平台；格式与平台解耦。
6. **事实可溯源、不确定就标注**：每条 concept 尽量带 `sources`；无法核实写"待核"，禁止编造来源、数字、时间。

## 2. 何时触发 / 不触发

**触发**：用户要"建知识库 / 沉淀这些资料 / 整理成知识包 / 项目长期记忆 / 外置记忆 / 新会话恢复上下文 / ingest / query / lint / 自检知识库"，或提到 OKF、Open Knowledge Format、Karpathy LLM Wiki、bundle/concept/frontmatter 知识格式。

**不触发**：一次性问答、无需持久化的闲聊、用户明确只要一次性格式转换而不要沉淀。

## 3. 核心概念（速览，权威定义看 SPEC）

- **bundle 知识包**：一个目录，是分发/交付单元；**concept**：包里一个 `.md`，文件路径去掉 `.md` 即 concept ID。
- 每个 concept 顶部是 `---` 包裹的 **YAML frontmatter**，**唯一必填字段是 `type`**；其余全部可选，且允许自定义 key，消费方不得因未知字段/缺可选字段报错。
- **index.md**：目录页，支持"渐进式披露"（先读它定位，再深读具体页，省 token）；**log.md**：倒序变更时间线。二者是保留文件名。
- **链接用普通 Markdown 链接**（推荐 bundle 根相对 `/x/y.md`），**不用 Obsidian `[[双链]]`**，保证任意系统都能读；允许死链（代表"尚未写"）。
- **v0.2 五个信任信号**（全可选）：`sources` 来源 / `generated`+`verified` 信任 / `stale_after` 时效 / `status` 生命周期 / `Attested Computation` 可鉴证计算（仅完整档）。

## 4. 四个操作（流程细节见 reference/ingest-query-lint.zh.md）

1. **init 初始化**：新项目用 `templates/bundle/` 生成骨架；**已有项目走第 6 节映射，不照搬骨架**。
2. **ingest 摄入**：读原始资料 → 与用户确认要点 → 建/改相关 concept（一次常牵动多页）→ 更新 index/log → 补 sources。原始资料不可变，单独存放。
3. **query 查询**：先读 index 定位 → 读相关 concept → 综合并带角注来源作答；高价值答案回写为新 concept，让探索复利。
4. **lint 自检**：查 frontmatter 可解析、缺 type、字段格式、死链、孤儿页、`stale_after` 过期、缺 sources；机械项跑 `scripts/okf_validate.py`，语义项人工判断。

## 5. 三档选用（默认标准档）

| 档位 | 字段 | 适用 |
|---|---|---|
| 最小档 | 仅 `type`（可加 title） | 轻量个人笔记、先占位 |
| **标准档（默认）** | type + title/description/tags + sources + generated/verified + status + stale_after | 课程知识库、项目长期记忆、绝大多数项目 |
| 完整档 | 标准档 + `type: Attested Computation`（runtime/parameters/executor/attester） | 仅财务/核心指标等"数字必须运行经批准代码"的强一致场景 |

字段逐项解释、示例与误区见 `reference/field-guide.zh.md`。

## 6. 接入已有项目（重要，例如 gaodun）

1. **先勘察**：读项目 AGENTS.md / README / 文档地图 / 现有索引与台账，列出"OKF 概念 ↔ 现有文件"映射表（bundle↔仓库、concept↔知识成品、index↔目录页/文档地图、log↔变更日志、schema↔AGENTS、动态记忆↔状态台账）。
2. **只叠加格式层**：给既有知识成品 `.md` 加 frontmatter；现有目录页当 index、现有变更记录当 log、现有 AGENTS 当 schema，**不新建平行知识库**。
3. **存量不强制回填**，今后新写的自然采用；改模板/治理规范属于项目的高扩散(L1)变更，必须先出方案 + 全量影响清单给用户确认，本地改完先不提交、验收后再 commit。
4. **篇内既有正文结构一律不动**（固定章节、脚本确定性生成的段落等）。若 frontmatter 与正文"信息块"内容重复，指定 **frontmatter 为机器权威、正文信息块由其派生**，并用校验器核对二者一致，避免两处维护漂移。
5. **同步外部平台时**（如飞书）：由同步环节负责把 frontmatter 渲染成人读信息块或剥离，外部读者不应看到裸 YAML；保证本地与外部同构。
6. **先声明采用边界**：项目如何采用/收窄 okf-wiki、如何覆盖全局默认，用 `templates/PROJECT-OKF-DECLARATION.template.md` 在项目 AGENTS 写一小节（项目规则优先于全局）；全新、完全按默认的项目可省略。

## 7. 跨会话外置记忆（细节见 reference/memory-handoff.zh.md）

- 把项目长期记忆做成一个 bundle：**schema**（项目 AGENTS.md 定规则）+ **index**（有什么、在哪）+ **log**（最近发生什么，倒序）+ **concepts**（稳定知识）。
- **新会话恢复顺序**：读项目 AGENTS.md(schema) → index → log 最近 N 条 → 按需深读相关 concept；不靠对话记忆猜测。
- 易变的值（进度计数、当前状态、日期、SHA）放台账/frontmatter **实时读取**，不抄进正文，防止"复制即过期"。
- 自动触发只能靠"项目 AGENTS.md 里的指针 + 用户开场话术"；豆包没有可由本技能改写的全局 AGENTS 文件时，明确告知用户、不伪造路径。

## 8. 文件指引（按需读取，不要一次全读）

| 需求 | 读这个 |
|---|---|
| 字段含义/三档/示例/误区 | `reference/field-guide.zh.md` |
| 跨会话记忆、新会话恢复 | `reference/memory-handoff.zh.md` |
| 执行 init/ingest/query/lint | `reference/ingest-query-lint.zh.md` |
| 字段权威定义、争议仲裁 | `reference/OKF-SPEC-v0.2.md`（内核，只读） |
| 新建 bundle/concept 骨架 | `templates/bundle/`、`templates/concepts/` |
| 声明项目采用边界、覆盖全局默认 | `templates/PROJECT-OKF-DECLARATION.template.md` |
| 机械校验 | `scripts/okf_validate.py` |
| 完整可运行样例 | `assets/example-bundle/` |

## 9. 交付前校验（强制）

```bash
python3 scripts/okf_validate.py <bundle目录>                   # E 必修、W 确认
python3 scripts/okf_validate.py <bundle目录> --strict          # W 也判不通过
python3 scripts/okf_validate.py <bundle目录> --max-warnings 5  # W 超过 5 条才不通过（分批清债）
python3 scripts/okf_validate.py <bundle目录> --json            # 机器可读
```
错误(E)必须修复；警告(W)列出并与用户确认。**实质改正文后旧 `verified` 不自动延续，需重新人工确认**（见 ingest SOP）。校验通过后仍要回读抽查 frontmatter 与正文一致性、index/log 是否同步，才算完成。

## 10. 许可与来源

- OKF SPEC © Google Cloud，Apache License 2.0，原文 `reference/OKF-SPEC-v0.2.md`，保留归属；本技能中文层为自用封装。
- 校验器参考最成熟的现成实现 `scaccogatto/okf-skills`（MIT，★373，2026-09-07 取数，Claude Code 原生）的一致性思路**零依赖重写**；并吸收其"实质改动 verified 不延续、批量回填不冒充人工、覆盖率对账、软/强制维护模式"等**纪律**，但不搬它的插件/Action/MCP/可视化/git 事件溯源（豆包不适用），来源见脚本头注释与 `reference/memory-handoff.zh.md` §6。
- 上游：规范现行权威仓 GoogleCloudPlatform/open-knowledge-format（v0.2）；理念来源 Andrej Karpathy "LLM Wiki" gist（2026-04）。
