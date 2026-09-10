# 四个操作 SOP：init / ingest / query / lint

> 对应 Karpathy LLM Wiki 的 Ingest/Query/Lint，加上初始化。流程细节在此，字段写法查 `field-guide.zh.md`，记忆恢复查 `memory-handoff.zh.md`。

---

## 0. 总原则

- **内核不改、三档选用、本地是源头、只记客观信号、不平行重建既有体系**（见 `../SKILL.md` 第 1 节）。
- 原始材料（视频转写、讲义、网页、PDF）**不可变**，单独存放（如 `references/` 或项目原始资源目录），concept 是"编译后"的成品，二者分开。
- 每次写入都可能牵动多页：新建 concept → 同步 index；内容变更 → 同步 log；交叉概念 → 互链。
- **三种工作模式**（对齐业界 produce/maintain/consume）：`produce` 从原料产出或扩展 bundle（=init+ingest）；`maintain` 现实变化后同步既有 concept（=更新型 ingest）；`consume` 读 bundle 作答（=query）。读与写边界要清楚：consume 不改文件，只有 produce/maintain 才落盘。

---

## 1. init —— 初始化一个 bundle

### 1.1 全新项目（没有既有文档体系）
1. 复制 `templates/bundle/` 骨架：根 `index.md`（带 `okf_version: "0.2"`）、`log.md`；按需建子目录。
2. 写项目 schema（即项目 `AGENTS.md`，可用 `templates/bundle/PROJECT-AGENTS.schema.md`）：定 type 词表、目录约定、读写顺序、三档选择。
3. 从 `templates/concepts/` 选档复制第一个 concept 模板。
4. 跑校验：`python3 scripts/okf_validate.py <bundle目录>`，应 0 error。

### 1.2 已有项目（如 gaodun）——**走映射，不照搬骨架**
1. 先勘察：读项目 AGENTS/README/文档地图/现有索引与台账。
2. 列"OKF 概念 ↔ 现有文件"映射表（bundle↔仓库、concept↔知识成品、index↔目录页/文档地图、log↔变更日志、schema↔AGENTS、动态记忆↔状态台账）。
3. 只叠加**格式层**：给知识成品加 frontmatter；现有目录页当 index、现有变更记录当 log。**存量不强制回填**，今后新写自然采用。
4. 篇内既有正文结构（固定章节、脚本确定性生成段落）一律不动。
5. 改模板/治理规范属项目高扩散(L1)变更：先出方案+全量影响清单给用户确认，本地改完先不提交、验收后再 commit。

---

## 2. ingest —— 把材料"编译"成知识

**输入**：一份或多份原始材料（讲义/转写/网页/对话结论/数据表）。
**输出**：新增或更新的若干 concept + 同步后的 index/log。

流程：
1. **通读原料、标来源**：确认每份材料的出处、时间、作者，准备好 `sources` 条目（稳定 id）。原料不可变、单独存。
2. **先对已有知识**：在 index 里查是否已有同主题 concept——有则**合并更新、跨源聚合**（同一知识点多讲/多卷聚到一篇），不重复建页；确无才新建。
3. **与用户确认要点**（关键/高风险内容）：提炼的结论先给用户过一眼再落盘，避免把理解偏差固化。
4. **写/改 concept**：
   - 选档（默认标准档）；`type` 必填，补 title/description/tags；
   - Answer-First：每段先给结论再展开；原子化，一段只讲一个问题；结构化 Markdown；
   - 关键论断用 `[^id]` 脚注逐句溯源；只记稳定结论，易变值留台账；
   - 填 `generated`；经用户确认加 `verified: human:xxx`；会过期的设 `stale_after`。
5. **织网**：在相关 concept 间加普通 Markdown 互链（允许先挂指向"尚未写"页的前向死链）。
6. **同步 index/log**：index 登记新页（`* [标题](链接) - 一句描述`）；log 顶部当天加 `**Creation/Update**`。
7. **校验**：跑 `okf_validate.py`，修掉所有 error，warning 与用户确认。

**ingest 禁忌**：不凭训练印象补事实；不把 AI 自己的错题/臆测当来源；冲突材料按项目权威度规则裁决而非覆盖。

> **实质改动后，旧 `verified` 不延续（重要）**：`verified`（谁人工核过）独立于 `generated.at`，只对"被核时的那版内容"负责。一旦正文被**实质性**修改（不是改错别字/格式），原人工核准自动失效——要么重新请人确认并更新 `verified`，要么先去掉它、让该页回到 machine-confirmed/unverified，**绝不能拿旧的人工核准给新内容背书**（SPEC §5.2）。

---

## 3. query —— 用知识库回答问题并回写复利

1. **先读 index 定位**（不要全量灌入）：找到相关 concept 路径。
2. **读相关 concept 正文 + frontmatter**：优先读 `status: stable`、信任等级高、未过 `stale_after` 的；对 draft/过期/未核验内容降权并标注。
3. **综合作答、带来源角注**：答案标明出自哪些 concept / sources；材料不足就说"知识库未覆盖"，不编造。
4. **高价值答案回写**：如果这次查询形成了可复用的新结论/新连接，按 ingest 回写成新 concept 或补进已有页——**让每次探索都让知识库更强（复利）**，而不是答完即弃。
5. 发现死链被本次补齐、或发现过期页，顺手记入待办并在用户同意后更新。

---

## 4. lint —— 知识库自检

分两层：**机械项跑脚本，语义项靠人/Agent 判断。**

### 4.1 机械项（`scripts/okf_validate.py`，确定性）
- [ ] 每个非保留 `.md` 都有可解析 frontmatter（**硬错误 E**）
- [ ] frontmatter 有非空 `type`（**硬错误 E**）
- [ ] `index.md`/`log.md` 结构合规（根 index 的 `okf_version`、log 日期为 YYYY-MM-DD 倒序等）
- 软警告 W（不致命，`--strict` 才当错误）：
  - [ ] 缺推荐字段 title/description/tags
  - [ ] 时间字段是否 ISO8601 带偏移；`status` 是否 draft/stable/deprecated
  - [ ] actor 形态（尤其人工是否正确用 `human:`）
  - [ ] 死链（**默认只提示不算错**，因可能是"尚未写"）
  - [ ] 孤儿页（没有被任何 index/概念链接到）
  - [ ] `stale_after` 已到期 / 缺 `stale_after` 的时效敏感页
  - [ ] 正文脚注 `[^id]` 是否都能在 `sources` 找到对应 id，反之是否有未使用的 source
  - [ ] v0.1 遗留：`timestamp`（应迁 `generated.at`）、正文 `# Citations`（应迁 `sources`）

```bash
python3 <skill>/scripts/okf_validate.py <bundle目录>                  # 常规：E 必改，W 列出确认
python3 <skill>/scripts/okf_validate.py <bundle目录> --strict         # 严格：W 也视为不通过
python3 <skill>/scripts/okf_validate.py <bundle目录> --max-warnings 5 # 允许遗留≤5条 W、超过才不通过（分批清债）
python3 <skill>/scripts/okf_validate.py <bundle目录> --json           # 机器可读输出
```

### 4.2 语义项（脚本查不出，人工/Agent 判断）
- [ ] 结论是否与来源一致、有无过度解读或幻觉
- [ ] 同一知识点是否跨页重复/矛盾（该合并未合并）
- [ ] frontmatter 与正文"信息块"是否一致（防止两处维护漂移；指定 frontmatter 为机器权威）
- [ ] 分组是否 MECE（不重不漏）、命名是否遵循项目约定
- [ ] 题量/计数等是否与权威台账/脚本结果对平
- [ ] 父节点(index)摘要是否随子页更新而刷新

### 4.3 修复纪律
- 硬错误 E 必须修复后才算完成；警告 W 逐条列出、与用户确认哪些修哪些保留（死链可能是有意的前向链接）。
- 批量修复属高扩散变更时，先给影响清单再动手。

---

## 5. 批量回填已有文档（backfill）纪律

> 场景：项目在采用 OKF 之前就已有大量知识成品（如 gaodun 知识详解 108 篇），需要批量补 frontmatter。借鉴最成熟的现成工具 okf-skills 的 backfill 设计，**只吸收纪律、不搬它的 git 事件溯源/多 Agent 重放（豆包单 Agent、且这些文件不进 git，用不上）**。

1. **只加元数据层，不动正文**：批量回填只在篇首插入/补全 frontmatter，既有章节结构、脚本确定性生成的段落一字不改；改不动结构就不算"回填"。
2. **机器回填不冒充人工核验（诚实性硬要求）**：脚本/AI 批量补出的条目，`generated.by` 如实标机器（如 `process:okf-backfill`），**不得写 `verified: human:`**——因此信任等级正确停在 unverified/machine-confirmed，后续由人逐篇核过才升级。批量"假装人工核过"等于伪造信任。
3. **按领域实体成篇，不按批次/变更命名**：一个知识点一篇、用知识点本名做 concept 文件名/标题，不要按"第几批回填""某次更新"这种变更维度造页。
4. **覆盖率要对账**：回填结束核对"应覆盖清单 vs 实际补全清单"，数量闭合（多少篇该补、补了多少、跳过多少及原因）；注意"已映射 ≠ 已精读"，解析存疑/被截断的要单列计数上报，不默认正确。
5. **低信号可跳过但要显式**：空文件、纯占位、重复文件等可跳过，但跳过规则要事先写明、结果可复核，不靠临场判断。
6. **回填是 L1 高扩散变更**：先出方案+全量影响清单给用户确认，先在少量篇目试点、回读确认 frontmatter 与正文信息块一致，再批量；本地改完先不提交，验收后再 commit。
