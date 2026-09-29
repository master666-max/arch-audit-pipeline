---
name: arch-audit-pipeline
description: 架构理解六步流程（含硬性审计门）。对既存代码库产出"供人理解的上层设计"：范围框定 → 静态制图 → 分层下钻 → 架构推断 → 架构审计 → 事实校验，审计动作嵌入每一步且 ⑤ 为独立质量门（Critical 未清零不得交付）。当用户要求理解/梳理仓库架构、画架构图（C4/Mermaid）、架构审计、模块划分分析、生成 onboarding 文档（AGENTS.md/模块卡片）、依赖分析或死代码识别时使用本技能。Orchestrates archify diagram authoring, Cognee codegraph, Explore subagent, and dependency auditors.
agent_created: true
---

# Arch Audit Pipeline：架构理解六步流程

## 核心原则：审计是硬性质量门，不是增值项

只描述"现状是什么"而不评估"合不合理"的架构图，是无法区分主干和肿瘤的解剖图：循环依赖照样画、死代码照样标成模块、分层名存实亡照样照实呈现，读者会误以为存在的就是合理的。因此：

- **每个步骤都嵌入审计动作**（见下表第 4 列），产出中间产物的 prompt 里必须包含对应审计指令；
- **⑤ 是独立关卡**：Critical 级问题（循环依赖、分层彻底失效等）未清零前，`architecture.md` 不得标记为"可交付"；
- **⑥ 反幻觉兜底**：报告里每条断言都要有代码证据（文件:行号 或 符号查找结果）。

## 流程总览

| # | 步骤 | 目标 / 产出 | 嵌入的审计动作 |
|---|------|------------|----------------|
| ① | 范围框定 | 锚定项目契约 → `AGENTS.md` | 技术栈与目录职责声明交叉验证；标注"声明 vs 推断"置信度 |
| ② | 静态制图 | 构建可检索的代码符号依赖图（持久化） | 与①声明对照，找出"文档说有但图里没有"的幽灵模块 |
| ③ | 分层下钻 | 模块卡片 map-reduce → `module-cards/*.md` | 每张卡片含"依赖指纹"字段，收集反向依赖不匹配项 |
| ④ | 架构推断 | 生成 C4/Mermaid → `architecture.md` | 依赖方向是否被代码强制；循环依赖检测；死代码识别（无人 import 的节点） |
| ⑤ | 架构审计 | 评估合理性而非仅描述 → `audit-report.md`（Critical/Major/Minor 三级） | 声明分层 vs 实际 import 偏离；热点聚集；扩展点是否被真实使用 |
| ⑥ | 事实校验 | 反幻觉核对 → `facts-checklist.md` | 与⑤的发现交叉核对，确保报告中每条断言有代码证据 |

主路 ①→⑥ 顺序执行；⑤ 发现 Critical 时进入阻断回路：标记 `architecture.md` 禁止交付 → 整改 → 重跑 ⑤。

## 流程图资产

- `assets/architecture-audit-pipeline.html` — 已交付的交互式流程图（三条泳道：依赖封装 / 六步主流程 / 审计阻断回路）。直接把该文件路径给用户，或复制到目标工作区展示。
- `assets/architecture-audit-pipeline.workflow.json` — archify workflow v2 源规格。需要改图（换语言、增删步骤）时编辑此文件并用 archify 重渲染：
  ```bash
  node <archify技能目录>/bin/archify.mjs deliver workflow <该json> <输出.html> --quality showcase --json
  ```
  修改后必须重跑 validate 与 visual-check（见 `references/archify-authoring-notes.md` 的实操要点与常见诊断）。

## 分步执行指令

### ① 范围框定
1. 读仓库根的 README / 包清单 / 已有 AGENTS.md，提取技术栈与目录职责**声明**。
2. 逐条标注置信度：声明与目录结构吻合 → 高；仅有声明无证据 → 标记"待②验证"。
3. 产出 `AGENTS.md`：技术栈、目录职责、构建/测试命令、显式未知项。

### ② 静态制图
1. 优先用 Cognee codegraph 流水线（MCP 或 `pip install cognee`）把代码库转为符号依赖知识图；不可用时降级：Tree-sitter/grep 扫 import/include 关系 + LSP 符号索引，产出等价的边列表（格式自由，但要可查询）。
2. 审计动作：拿①的声明清单逐项对图，输出"幽灵模块"清单（文档有、图里无）与"图有、文档无"的未声明模块。

### ③ 分层下钻
1. 用 Explore 类子代理做 map-reduce：每个模块一张卡片（职责、对外接口、依赖指纹）。
2. "依赖指纹" = 该模块 import 的模块列表 + 被谁 import 的反向列表。
3. 审计动作：反向依赖中出现跨层引用（如下层 import 上层）→ 记入不匹配清单。

### ④ 架构推断
1. 基于②的图与③的卡片生成 `architecture.md`：分层图（C4 语境图 + 容器图，或 Mermaid）+ 每层职责说明。
2. 审计动作（必做，写进生成 prompt）：
   - **方向审计**：每条箭头方向是否有代码强制（import 方向、接口调用方向）；
   - **循环依赖**：在②的图上跑环检测；
   - **死代码**：入度为 0 且非入口（main/路由/导出）的节点。

### ⑤ 架构审计（独立关卡）
1. 跑工具审计：Java 用 ArchUnit；JS/TS 用 dependency-cruiser；Python 用 import-linter。规则集：分层约定、禁止循环、禁止跨层。
2. 补充 LLM 审查（Superpowers 请求代码审查或内置审查能力），按 **Critical / Major / Minor** 三级输出 `audit-report.md`：
   - Critical：循环依赖、分层彻底失效、声明的核心扩展点完全未被使用；
   - Major：跨层反向引用、热点文件聚集大量声明外耦合；
   - Minor：命名与分层不符、孤立工具模块。
3. **硬门**：存在未解决的 Critical → 在 `architecture.md` 顶部标记"不可交付（阻断原因：…）"，进入整改回路；否则放行到 ⑥。

### ⑥ 事实校验
1. 对 `architecture.md` + `audit-report.md` 的每条关键断言做符号级核对（Serena / Cognee 检索，或 grep + 行号）。
2. 产出 `facts-checklist.md`：断言 → 证据（文件:行号）→ 结论（证实/证伪/存疑）。证伪项回写修正报告后，流程才算交付。

## 依赖封装与降级策略

完整清单（安装方式、备选、成熟度证据）见 `references/dependency-inventory.md`。摘要与降级：

| 能力 | 首选 | 缺失时降级 |
|------|------|-----------|
| 流程骨架 + 审查门 | Superpowers 插件（头脑风暴 / 请求代码审查）或本机 superpowers-zh 技能 | 用内置规划与审查能力，手动执行⑤的三级分级 |
| 代码知识图 | Cognee codegraph（MCP / pip） | Tree-sitter/grep + LSP 建边列表 |
| 模块下钻 | Explore 子代理 | 直接分批读目录，人工归纳卡片 |
| 符号查找 | Serena MCP | Cognee 检索或 grep -n |
| 依赖规则审计 | ArchUnit / dependency-cruiser / import-linter | 在②的边列表上自写环检测与分层校验脚本 |

封装是加速器，不是前置条件：任何环境下六步都必须完整走完，审计门不可裁剪。

## 交付物清单

一次完整运行应产出：`AGENTS.md`、代码依赖图（持久化）、`module-cards/*.md`、`architecture.md`、`audit-report.md`、`facts-checklist.md`，以及本技能 assets 中的流程图（渲染到目标工作区）。

## 参考文件

- `references/dependency-inventory.md` — 插件 / MCP / Skill / CLI 完整依赖清单：版本证据、安装命令、备选方案、适配判断。
- `references/archify-authoring-notes.md` — 用 archify 落地本流程图的实操要点：泳道布局、路由诊断、标签宽度、视口包含性的已验证修法。
