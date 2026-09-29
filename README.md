# arch-audit-pipeline

架构理解六步流程（含硬性审计门）—— ZCode / CodeBuddy 标准 Skill。

对既存代码库产出"供人理解的上层设计"：**范围框定 → 静态制图 → 分层下钻 → 架构推断 → 架构审计 → 事实校验**。审计动作嵌入每一步，⑤ 为独立质量门：Critical 未清零，`architecture.md` 不得标记为可交付。⑥ 对报告做符号级反幻觉核对。

## 安装

复制本目录到 `~/.zcode/skills/arch-audit-pipeline`（用户级）后重启会话即可被技能调度识别。

## 目录

```
SKILL.md                                  # 六步流程主指令（含每步嵌入的审计动作与降级策略）
references/dependency-inventory.md        # 插件/MCP/Skill/CLI 完整依赖清单与安装方式
references/archify-authoring-notes.md     # archify 落地本流程图的实操要点（已验证修法）
assets/architecture-audit-pipeline.html   # 交互式流程图（三泳道：依赖封装/主流程/阻断回路）
assets/architecture-audit-pipeline.workflow.json  # archify workflow v2 源规格，可改后重渲染
```

## 依赖

首选封装：Superpowers（流程骨架+审查门）、Cognee（持久代码知识图）；备选与降级策略见 `references/dependency-inventory.md`。封装缺失时六步仍须完整走完——审计门不可裁剪。

## 流程图

![preview](assets/architecture-audit-pipeline.html)

（HTML 为交互式交付物，克隆后在浏览器打开；改 `workflow.json` 后用 archify `deliver` 重渲染。）
