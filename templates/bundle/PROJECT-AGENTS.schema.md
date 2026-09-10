# 项目 AGENTS / schema 模板（教 AI 如何读写本 bundle）

> 本文件是 OKF 的 **schema 层**（对应 Karpathy LLM Wiki 的 schema）。新项目把它落成项目根 `AGENTS.md`；
> 已有项目（如 gaodun）**不要新建**，只在既有 AGENTS.md 加一小节指针指向 okf-wiki 技能即可。
> 保持精简——它每次新会话都要读，过长会降低遵守度（全局经验：规则文件控制在必要长度）。

## 1. 这个项目是什么
{一句话：目标、读者、bundle 根路径}

## 2. 目录与 type 词表（约定，不集中注册但项目内统一）
- 目录约定：{如 concepts/ 放知识、playbooks/ 放流程、references/ 放外部材料镜像}
- type 词表：{Concept / Reference / Playbook / Decision / …，各自含义}
- 命名：{文件/目录命名规则；concept ID = 相对路径去掉 .md}

## 3. 本项目采用哪一档
{默认标准档；哪些类型用最小档；是否存在需要完整档(Attested Computation)的场景——多数项目写"无"}

## 4. 新会话恢复顺序（强制）
1. 读本 AGENTS(schema) → 2. 读根 index 定位 → 3. 读 log 最近 N 条与动态台账 → 4. 按需深读相关 concept，不整库注入。
5. 读到内容先看 verified / stale_after，过期或未核验先标注。

## 5. 写入与同步规则
- 稳定结论进 concept（标准档 frontmatter）；易变状态（进度/计数/当天日期）进台账实时读，不抄进正文。
- 新建/改名 concept 必须同步 index；每次变更在 log 顶部当天追加 Creation/Update/Deprecation。
- 关键结论带 sources 脚注；AI 产出记 generated，经人确认加 verified: human:xxx；会过期设 stale_after。
- 冲突按权威度裁决、退役标 status: deprecated 不硬删。

## 6. 交付前校验
`python3 ~/Doubao/skills/okf-wiki/scripts/okf_validate.py <bundle根> [--strict]`：error 必修，warning 逐条确认；
另做语义自检（重复/矛盾、frontmatter 与正文信息块一致、父节点摘要随子页刷新）。

## 7. 外部同步（如有，如飞书）
{本地 bundle 是唯一源头，校验通过后再同步外部；同步环节负责把 frontmatter 渲染成人读信息块或剥离，外部读者看不到裸 YAML。}
