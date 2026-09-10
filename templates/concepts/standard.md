---
type: Concept                       # 必填。按项目 type 词表填，如 Concept/Reference/Playbook/Decision
title: "{知识点/概念名}"
description: "{一句话摘要，供 index 与搜索片段使用}"
tags: [标签1, 标签2]                  # 跨切面分类，项目统一词表
# resource: "https://或/bundle/内路径"  # 仅当描述某个真实底层资产（表/接口/文件）时才填，抽象知识留空
sources:                            # 来源族：这篇从哪些材料提炼（只记客观信号，不存可信度分数）
  - id: src-1                       # 稳定 id，正文用 [^src-1] 脚注逐句溯源；勿用数组下标
    resource: "{URL 或 bundle 内路径，如 ./references/xxx.pdf}"
    title: "{来源标题}"
    author: "human:{作者id}"         # 来源作者，actor 写法；机器源用 名称/版本
    last_modified: 2026-09-01T00:00:00+08:00   # 来源本身最后变更（区别于下方 generated.at）
generated: { by: "doubao/okf-wiki", at: 2026-09-07T15:30:00+08:00 }  # 谁在何时写出/最后实质修改
verified: { by: "human:{审核人id}", at: 2026-09-07T20:00:00+08:00 }   # 谁核过；没核可先删本行=unverified
status: stable                      # draft 草稿 | stable 可用(缺省) | deprecated 已退役
stale_after: 2026-12-31T23:59:59+08:00   # 绝对时刻，到期需复审；长期有效可删本行
# —— 项目自定义 key（示范：gaodun 知识详解可加，消费方不得因未知 key 报错）——
# group: "08_资源税·环境保护税"
# season: "26考季"
---

# {知识点/概念名}

> 所属：{组/模块}　来源：{讲义 a 份 · 转写 b 讲 · 题 c 道}　适用：{范围/考季}
> （本信息块由 frontmatter 派生，保持一致，避免两处维护漂移）

## 一、核心内容
{Answer-First：先结论后展开；一段只讲一个问题；优先列表/表格}

关键论断示例，逐句挂来源脚注。[^src-1]

[^src-1]: {来源标题，与 sources[].id 对应}

## 二、应用 / 考试或实操指导
{考情、易错、口诀、操作要点等；与客观知识分节}

## 关联
- [{相邻概念}](./相邻概念.md)：{一句关联说明}（允许指向尚未写的页）
