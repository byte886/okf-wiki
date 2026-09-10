# OKF 字段中文手册（三档选用 · 示例 · 误区）

> 权威定义以 `../reference/OKF-SPEC-v0.2.md` 为准（本手册只做中文释义与使用建议，不修改规范）。
> 阅读顺序：先看「一、最小骨架」→ 再按「三档」决定写到哪一档 → 写具体字段时查「二、字段逐项」。

---

## 一、最小骨架（30 秒）

一个 concept 就是一个 `.md`，分两部分：顶部 `---` 包裹的 YAML（frontmatter）+ 下面的 Markdown 正文。

```markdown
---
type: Concept          # 唯一始终必填；其余全部可选
title: 显示名（可选）
description: 一句话摘要（可选）
tags: [标签1, 标签2]    # 可选
---

# 正文标题
正常写 Markdown。
```

**合规底线（SPEC §11，只有这三条是硬错误）**：
1. 每个非保留 `.md`（即除 `index.md`/`log.md` 外）都要有能解析的 frontmatter；
2. frontmatter 里要有非空的 `type`；
3. `index.md`/`log.md` 出现时要符合 §8/§9 结构。

> 只写一个 `type` 就是**完全合规**的 OKF 文档。缺任何可选字段、`type` 取了没见过的值、出现没见过的自定义 key、链接指向尚不存在的页、缺 index.md——**都不许报错、不许拒绝读取**（§11）。这是 OKF "宽容消费"的核心哲学。

---

## 二、字段逐项

### 2.1 基础字段（§4.1）

| 字段 | 必填 | 含义与写法 | 建议 |
|---|---|---|---|
| `type` | **是** | 概念种类的短字符串，用于路由/过滤/展示，如 `Concept`/`Reference`/`Playbook`/`Metric`/`Attested Computation`。**不集中注册**，自己起描述性名字即可，消费方要容忍未知 type | 一个项目内尽量统一一套 type 词表，写进项目 schema（AGENTS） |
| `title` | 否 | 人读显示名；缺省时可用文件名推导 | 标准档建议填，便于 index 自动生成 |
| `description` | 否 | 一句话摘要，供 index、搜索片段、预览使用 | 标准档建议填，是"渐进式披露"省 token 的关键 |
| `resource` | 否 | 该概念所描述**底层资产**的规范 URI（表、接口、文件、网页）。描述抽象想法、没有对应实体资产时**留空** | 有真实外部对象才填；纯方法论/笔记不填 |
| `tags` | 否 | YAML 列表，跨切面分类标签 | 用短词、全项目统一词表 |
| 任意自定义 key | — | 允许生产者加任意字段；消费方应原样保留、不得因未知字段拒绝 | 项目特有元数据（如 gaodun 的"所属组/考季"）用自定义 key 承载，不要硬塞进标准字段 |

正文（§4.2）是自由 Markdown，**没有必填小节**；`# Schema`/`# Examples`/`# Computation` 是约定俗成的标题，适用时再用。结构化 Markdown（标题、列表、表格、代码块）优于大段散文。

### 2.2 来源族 `sources`（§5.1，标准档起）

记录这篇概念"从哪些材料提炼"。**只记客观信号，不存可信度分数**（分数主观、不可移植、会过期；可信度由读者看到来源后自己判断）。

```yaml
sources:
  - id: lecture-zhuzishui            # 稳定 id：正文角注靠它关联，别用数组下标
    resource: ./原始讲义/资源税法讲义.pdf   # 必填：URL / bundle 内路径 / references/ 路径 / 范围描述
    title: 资源税法和环境保护税法讲义
    author: human:caijunjun          # 来源作者，用 actor 写法（§2.5）
    last_modified: 2026-08-01T00:00:00+08:00   # 来源本身最后变更时间（区别于 generated.at 写作时间）
    usage_count: 62                  # 可选：被使用/引用次数（活跃度信号，非精确评分）
usage_window: { from: 2026-06-01T00:00:00+08:00, to: 2026-08-31T23:59:59+08:00 }  # 给 usage_count 限定统计窗口
```

- 每个 source 条目里 **`resource` 必填**；`id` 在正文要引用该来源时应填；`title/author/usage_count/last_modified` 均可选。
- **逐句溯源用 Markdown 脚注，label = `sources[].id`**：

  ```markdown
  进口应税资源不征资源税。[^lecture-zhuzishui]

  [^lecture-zhuzishui]: 资源税法和环境保护税法讲义
  ```
- **为什么用稳定 id 而不是 `sources[0]` 位置下标**：Agent 会频繁重排文档，下标一重排就静默错配来源；稳定 id 重排后仍对得上。
- 血缘关系靠"链接"表达，不设专门字段；更深的外部数据血缘 v0.2 不做。

### 2.3 信任族 `generated` / `verified`（§5.2，标准档起）

"谁写的"和"谁核过的"分开记——写的人不一定是核的人。

```yaml
generated: { by: doubao/okf-wiki, at: 2026-09-07T15:30:00+08:00 }   # by 必填；at=最后一次实质修改时间
verified:                                                          # 列表，可多次独立核验
  - { by: human:wenjiechen, at: 2026-09-07T20:00:00+08:00 }
# 只有一个核验者时可简写为单个映射，消费方必须当作单元素列表：
verified: { by: human:wenjiechen, at: 2026-09-07T20:00:00+08:00 }
```

**信任等级是"读取时现算"，绝不写进文档（§5.3）**：

| frontmatter 状态 | 现算信任等级 |
|---|---|
| 没有 `verified` | **unverified**（未核验，仍可用，不得拒绝） |
| `verified` 全是非 `human:`（agent/process） | **machine-confirmed**（机器确认） |
| `verified` 里出现过 `human:xxx` | **human-reviewed**（人工复核，最高） |

### 2.4 生命周期 `status` / `stale_after`（§5.4/§5.5，标准档起）

```yaml
status: stable          # draft 草稿未审 | stable 可用（缺省值）| deprecated 留链存档但已过时
stale_after: 2026-12-31T23:59:59+08:00   # 绝对时刻；now >= 它即"过期"。写绝对时间而非相对 TTL
```

- 不写 `status` 视为 `stable`。
- **`stale_after` 必须是绝对 ISO8601 时刻，不写"30 天后"这种相对量**：这样判断过期只需和当前时间比较，与"何时读到它"无关。
- 典型用法：考季型内容（如"26 考季"）把 `stale_after` 设到该考季结束/新教材发布点，lint 时自动提示复审。

### 2.5 Actor 写法（§7）

凡是记录身份的字段（`generated.by`、`verified[].by`、`sources[].author`）统一三种形态：

| 形态 | 用于 | 例子 |
|---|---|---|
| `<生产者>/<版本>` | Agent / 工具 | `doubao/okf-wiki`、`reference_agent/gemini-2.5-pro` |
| `human:<id>` | **人**（手写或人工复核必须用此前缀，信任等级靠它识别） | `human:wenjiechen` |
| `process:<id>` | 自动化流程 | `process:nightly-qa` |

> ⚠️ 易错：把人写成 `human/wenjie`（斜杠）或 `Human:xx`（大小写/拼写），会被当成 Agent，信任等级被静默降为 machine-confirmed。人工内容必须是精确的 `human:` 前缀。

### 2.6 时间格式（§5 开头，硬约定）

所有时间值都是 **ISO 8601 且带明确 UTC 偏移**：`2026-06-30T14:00:00Z`（Z=UTC）或 `2026-06-30T22:00:00+08:00`。中国时区统一写 `+08:00`。日期-only（`2026-09-07`）可被宽容接受但损失精度，不推荐。

### 2.7 完整档：Attested Computation 可鉴证计算（§10，一般用不到）

`type: Attested Computation` 的概念，额外携带"这个数字必须按批准的方式算出来"的契约：

| 字段 | 说明 |
|---|---|
| `runtime` | **该 type 必填**，决定参数语义：`bigquery`/`postgres`/`dbt`/`python`/`Looker` 等 |
| `parameters` | 允许 Agent 填值的"类型化空洞"列表，每项 `{ name, type, required }` |
| `computation` | 计算本体的路径（可选）；缺省时用正文 `# Computation` 代码块 |
| `executor` | `{ resource, receipt }`：怎么跑、跑完必须回传哪些凭据（如 job_id、实际执行的 SQL、结果） |
| `attester` | `{ resource }`：**无 LLM 的确定性校验代码**，拿 receipt 出结论 |

**铁律：Agent 只能给声明好的 `parameters` 填值，绝不能编写或修改 `computation` 本身。**
- `verified` 管"定义是否仍符合口径"（文档级、慢、存进 bundle）；attestation 管"这一次运行是否按批准方式算出该值"（每次运行、运行时、不存进 bundle）。两者都要，不互相替代。
- **适用判断**：只有"财务数字/核心指标必须由经批准代码产出、禁止 Agent 自己估"时才用完整档。课程知识库、个人/项目笔记用标准档即可，**gaodun 明确不选完整档**。

---

## 三、三档选用（默认标准档）

| 档位 | frontmatter 字段 | 适用场景 | gaodun |
|---|---|---|---|
| **最小档** | `type`（可加 title） | 快速占位、轻量个人笔记、先建页后补元数据 | 早期草稿可临时用 |
| **标准档（默认）** | type + title/description/tags + sources + generated/verified + status + stale_after（+ 项目自定义 key） | 课程/知识库、项目长期记忆、绝大多数长期项目 | **知识详解成品用这档** |
| **完整档** | 标准档 + Attested Computation 五字段 | 财务/核心指标强一致、数字必须可复算 | 不选 |

**"项目选择性使用"靠选档实现，不靠删 SPEC、不靠裁剪字段。** 同一 bundle 内允许不同文档处于不同档（草稿最小档、定稿标准档）。

---

## 四、index.md 与 log.md（保留文件，§8/§9）

### index.md（目录页，支持渐进式披露）
- **无 frontmatter**，唯一例外：bundle 根的 index.md 可写 `okf_version: "0.2"`（这是 index 唯一被允许的 frontmatter）。
- 正文按小节分组，条目格式：`* [标题](相对链接) - 一句描述`（描述直接取被链概念的 description）。

```markdown
---
okf_version: "0.2"
---

# 模块组

* [资源税法](资源税法.md) - 境内开发应税资源的征税规则与计算，62 题。
* [环境保护税法](环境保护税法.md) - 直接向环境排放应税污染物的征税规则。
```

### log.md（变更时间线，倒序）
- 用 `## YYYY-MM-DD` 分组，**最新在最上面**；条目是散文，行首加粗词 `**Creation**/**Update**/**Deprecation**` 是约定非强制。

```markdown
# Directory Update Log

## 2026-09-07
* **Creation**: 建立 [资源税法](资源税法.md)。
* **Update**: 按 26 考季讲义补充水资源税试点内容。
```

---

## 五、链接与路径（§6）

- 用**普通 Markdown 链接**，**不用 Obsidian `[[双链]]`**（保证任意工具都能读）。
- 推荐 **bundle 根相对**（以 `/` 开头），文档在子目录间移动时更稳：`[客户表](/tables/customers.md)`；也允许普通相对链接 `./other.md`。
- **必须容忍死链**：指向尚不存在的页不算错误，它表达"这页以后要写"。
- `resource`/`sources[].resource`/`computation`/`executor.resource`/`attester.resource` 这些路径字段接受：绝对 URL、`/` 开头的 bundle 相对路径、普通相对路径。
- `references/` 是约定目录：把外部材料、运行说明、代码作为"一等概念"镜像进来，供 sources/executor/attester 指向；非强制。

---

## 六、常见误区（务必避开）

1. ❌ 自己存一个 `trust_score: 0.9` / `credibility: high` → ✅ 信任等级、过期与否**读取时由字段现算**，不写死。
2. ❌ 用 `[[资源税法]]` 双链 → ✅ 用标准 Markdown 链接。
3. ❌ 来源用 `[1]` 位置下标、重排后错配 → ✅ 用稳定 `id` + 同名脚注。
4. ❌ 缺可选字段/遇到未知 type 就报错拒绝 → ✅ 宽容消费，当普通概念处理。
5. ❌ 把人写成 `human/xxx` 导致信任被降级 → ✅ 人工一律 `human:xxx`。
6. ❌ `stale_after: 30天后`（相对 TTL）→ ✅ 写绝对 ISO8601 时刻。
7. ❌ 为了"完整"给笔记硬套 Attested Computation → ✅ 没有"必须经批准代码复算"的强需求就别用完整档。
8. ❌ 把易变的计数/当前状态/日期抄进正文 → ✅ 这类值放台账/frontmatter 实时读，正文只记稳定结论（防"复制即过期"）。
9. ❌ v0.1 老文档直接判废 → ✅ `timestamp` 可回退为 `generated.at`、正文 `# Citations` 可回退为 `sources`（§13.1），逐步迁移。
