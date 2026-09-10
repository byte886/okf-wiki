---
type: Concept
title: 什么是 OKF
description: OKF（Open Knowledge Format）是用 Markdown 正文加 YAML frontmatter 表达知识的开放、跨厂商格式。
tags: [okf, 知识管理, 入门]
resource: https://github.com/GoogleCloudPlatform/open-knowledge-format
sources:
  - id: spec
    resource: ../../reference/OKF-SPEC-v0.2.md
    title: Open Knowledge Format Specification v0.2
    author: team:google-cloud
    last_modified: 2026-08-21T00:00:00Z
generated: { by: "doubao/okf-wiki", at: 2026-09-07T15:30:00+08:00 }
verified: { by: "human:wenjiechen", at: 2026-09-07T20:00:00+08:00 }
status: stable
stale_after: 2027-03-31T23:59:59+08:00
---

# 什么是 OKF

## 核心结论
OKF 不是新软件，而是一套**文件格式约定**：每个知识点是一个 Markdown 文件，顶部用 YAML 写元数据，正文写内容；唯一始终必填的字段是 `type`。[^spec]

## 关键设计
- **渐进式披露**：先读 `index.md` 目录定位，再打开具体条目，省 token。
- **信任可现算**：不写死可信度分数，由 `sources`/`generated`/`verified` 在读取时判断。
- **宽容消费**：缺可选字段、未知 type、死链都不许报错拒绝。

## 关联
- 同目录还有一篇[最小占位笔记](./minimal-note.md)，演示只写 `type` 的最小合规形态。

[^spec]: Open Knowledge Format Specification v0.2
