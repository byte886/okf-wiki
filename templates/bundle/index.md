---
okf_version: "0.2"
---
# {bundle / 目录名}

> 目录页（index）：只做导航、不承载正文。支持"渐进式披露"——先读本页定位，再打开具体 concept。
> 根 index 是唯一允许带 frontmatter 的 index（仅 okf_version）；子目录 index 不带 frontmatter。
> 条目格式固定：* [标题](相对链接) - 一句描述（描述取该 concept 的 description）。

# {分组一，如：核心概念}

* [概念A](concepts/a.md) - 一句话描述 A。
* [概念B](concepts/b.md) - 一句话描述 B。

# {分组二，如：方法论 / SOP}

* [某流程](playbooks/x.md) - 一句话描述。

# 子目录

* [references](references/) - 外部材料与代码镜像。
