# 项目采用声明模板（PROJECT-OKF-DECLARATION）

> **用途**：在某个具体项目的 `AGENTS.md` 里加一小节，声明本项目"怎么用 okf-wiki、用到什么程度、哪些全局默认不采用"。
> **与 `bundle/PROJECT-AGENTS.schema.md` 的分工**：后者是从零搭一份项目 AGENTS 的**完整骨架**；本文件只是其中"okf-wiki 采用边界"那一小节——**已有 AGENTS 的项目用它做增量追加，不重写 AGENTS**。
> **为什么需要**：全局 `~/Doubao/AGENTS.md` 对所有项目生效（默认套餐），仲裁顺序为 当次指令 ＞ 项目 AGENTS ＞ 全局 AGENTS ＞ PROFILE ＞ 平台记忆。项目要偏离默认、或已有记忆体系怕冲突，就靠本声明把本地规矩写明，AI 不必临场猜。**全新且完全接受默认的项目可省略本声明。**
> 保持几行即可——它每次新会话都要读，过长会降低遵守度。

## 可直接复制（替换尖括号占位）

```markdown
## 本项目如何使用 okf-wiki（采用边界，覆盖全局默认）
- 现有体系：以 <现有文档地图 / CHANGELOG / active 台账> 为准；okf-wiki 只承担 <只叠加 frontmatter 格式层 / 完整承担知识库与外置记忆 / 不使用>，不新建平行记忆目录。
- 知识位置：bundle 根=<路径>；index=<目录页>，log=<变更记录>，动态台账=<易变状态，实时读>。
- 采用档位：<最小 / 标准(默认) / 完整>；项目自定义 type 词表与键：<如 type: 知识点；group/season；无则写"无">。
- 不采用全局默认中的：<如 存量不回填 / 不设 stale_after / 不挂校验门；没有写"无">。
- 冲突处理：与 ~/Doubao/AGENTS.md 或技能默认不一致时，一律以本文件为准。
```

## 两个填法示例

**已有成熟体系（收着用，只加格式层）**
```markdown
## 本项目如何使用 okf-wiki（采用边界，覆盖全局默认）
- 以 docs/DOCUMENTATION_MAP + CHANGELOG + project-management/active 台账为准；okf-wiki 只给知识页加标准档 frontmatter，不新建目录。
- 知识位置：data/.../知识详解；index=各组 README 与 DOCUMENTATION_MAP，log=CHANGELOG，台账=active/TASK_STATUS。
- 存量默认不回填；不采用 Attested Computation；其余冲突以本 AGENTS 为准。
```

**全新小项目（放开用）**
```markdown
## 本项目如何使用 okf-wiki（采用边界，覆盖全局默认）
- 完整使用 okf-wiki；bundle 根=./knowledge（index.md / log.md / concepts/），易变状态放 ./knowledge/active。
- 默认标准档，无 Attested Computation 场景；冲突以本 AGENTS 为准。
```

## 让 AI 帮你加到某个项目（话术）

在**那个项目的对话窗口**里说。先出草稿、确认后再写（治理变更先确认，不直接改文件）：

> 按 okf-wiki：读取 `~/Doubao/skills/okf-wiki/templates/PROJECT-OKF-DECLARATION.template.md`，勘察本项目现有的 AGENTS / README / 文档地图 / 台账，先产出一份"本项目采用声明"草稿、并说明将插入 AGENTS 的哪个位置给我确认，**先不要改文件**。

确认无误后再说：

> 就按这版写入项目 AGENTS.md，并在该登记的索引处同步登记。
