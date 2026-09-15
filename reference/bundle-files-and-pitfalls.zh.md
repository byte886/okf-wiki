# OKF 三档选用 · index/log · 链接规则 · 常见误区

> 权威定义以 `OKF-SPEC-v0.2.md` 为准（本手册只做中文释义与使用建议，不修改规范）。
> 字段逐项含义、最小骨架、YAML 示例见 [field-guide.zh.md](field-guide.zh.md)。

---

## 一、三档选用（默认标准档）

| 档位 | frontmatter 字段 | 适用场景 | gaodun |
|---|---|---|---|
| **最小档** | `type`（可加 title） | 快速占位、轻量个人笔记、先建页后补元数据 | 早期草稿可临时用 |
| **标准档（默认）** | type + title/description/tags + sources + generated/verified + status + stale_after（+ 项目自定义 key） | 课程/知识库、项目长期记忆、绝大多数长期项目 | **知识详解成品用这档** |
| **完整档** | 标准档 + Attested Computation 五字段 | 财务/核心指标强一致、数字必须可复算 | 不选 |

**"项目选择性使用"靠选档实现，不靠删 SPEC、不靠裁剪字段。** 同一 bundle 内允许不同文档处于不同档（草稿最小档、定稿标准档）。

---

## 二、index.md 与 log.md（保留文件，§8/§9）

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

## 三、链接与路径（§6）

- 用**普通 Markdown 链接**，**不用 Obsidian `[[双链]]`**（保证任意工具都能读）。
- 推荐 **bundle 根相对**（以 `/` 开头），文档在子目录间移动时更稳：`[客户表](/tables/customers.md)`；也允许普通相对链接 `./other.md`。
- **必须容忍死链**：指向尚不存在的页不算错误，它表达"这页以后要写"。
- `resource`/`sources[].resource`/`computation`/`executor.resource`/`attester.resource` 这些路径字段接受：绝对 URL、`/` 开头的 bundle 相对路径、普通相对路径。
- `references/` 是约定目录：把外部材料、运行说明、代码作为"一等概念"镜像进来，供 sources/executor/attester 指向；非强制。

---

## 四、常见误区（务必避开）

1. ❌ 自己存一个 `trust_score: 0.9` / `credibility: high` → ✅ 信任等级、过期与否**读取时由字段现算**，不写死。
2. ❌ 用 `[[资源税法]]` 双链 → ✅ 用标准 Markdown 链接。
3. ❌ 来源用 `[1]` 位置下标、重排后错配 → ✅ 用稳定 `id` + 同名脚注。
4. ❌ 缺可选字段/遇到未知 type 就报错拒绝 → ✅ 宽容消费，当普通概念处理。
5. ❌ 把人写成 `human/xxx` 导致信任被降级 → ✅ 人工一律 `human:xxx`。
6. ❌ `stale_after: 30天后`（相对 TTL）→ ✅ 写绝对 ISO8601 时刻。
7. ❌ 为了"完整"给笔记硬套 Attested Computation → ✅ 没有"必须经批准代码复算"的强需求就别用完整档。
8. ❌ 把易变的计数/当前状态/日期抄进正文 → ✅ 这类值放台账/frontmatter 实时读，正文只记稳定结论（防"复制即过期"）。
9. ❌ v0.1 老文档直接判废 → ✅ `timestamp` 可回退为 `generated.at`、正文 `# Citations` 可回退为 `sources`（§13.1），逐步迁移。
