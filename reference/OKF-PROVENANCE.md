# OKF 内核来源与许可说明（Provenance）

> 本文件由 okf-wiki 技能维护，记录内置规范内核的出处、版本与核对情况。**规范正文以 `OKF-SPEC-v0.2.md` 为准，本文件只做来源登记。**

## 规范身份

- 名称：Open Knowledge Format（OKF，开放知识格式）
- 内置版本：**v0.2**
- 内核文件：`reference/OKF-SPEC-v0.2.md`（1006 行，逐字内置，禁止改写）

## 上游仓库与迁移情况

- **现行权威仓库**：[`GoogleCloudPlatform/open-knowledge-format`](https://github.com/GoogleCloudPlatform/open-knowledge-format)（规范、参考 agent、样例 bundle 的正式归属地；默认分支 `main`）。
- 历史路径：规范最早位于 `GoogleCloudPlatform/knowledge-catalog` 仓库的 `okf/` 目录；该目录后被标注为**冻结快照、不再维护**，权威迁至上述独立仓库。
- 出品方：Google Cloud（仓库同时带有 "not an official Google product" 类声明；v0.x 仍属草案阶段，字段可能在后续版本调整）。

## 本次核对（2026-09-07，UTC+8）

- 从现行权威仓 `main/SPEC.md` 重新拉取，与内置文件逐行 `diff`：**差异 0 行，完全一致**（同为 Version 0.2、1006 行）。
- 即：虽然内置文件最初下载自旧仓 `knowledge-catalog/okf/SPEC.md`，但其内容与现行权威仓 v0.2 完全相同，无需替换。
- 许可证文件：`reference/OKF-LICENSE-Apache2.0.txt`（Apache License 2.0 全文，来自权威仓 `LICENSE.md`）。

## 跟版升级规则

1. OKF 发布新版本（如 v0.3 / v1.0）时，从**现行权威仓**重新下载 `SPEC.md`，**整体替换** `OKF-SPEC-v0.2.md`（并按新版本号重命名）。
2. 替换后重新 `diff` 留痕，更新本文件的"本次核对"；若字段有 breaking change，同步修订 `field-guide.zh.md`、模板与 `scripts/okf_validate.py`。
3. 技能的中文"壳"可以迭代，但内核 SPEC 永远保持上游逐字，不在本地做增删改。

## 理念来源

- Andrej Karpathy，《LLM Wiki》gist（2026-04-04）：提出"把知识编译成 Markdown wiki、由 LLM 持续维护"的理念（raw/wiki/schema 三层，ingest/query/lint）。OKF 是这一理念的**跨厂商格式标准**落地。
