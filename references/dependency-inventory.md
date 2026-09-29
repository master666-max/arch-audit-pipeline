# 依赖封装清单：插件 · MCP · Skill · CLI

按类别列出六步流程依赖的全部封装，附安装方式与降级备选。成熟度判断依据上游仓库活跃度、star 数、许可证与多 harness 兼容性（截至 2026-09）。

## 1. 插件

### Superpowers（obra/superpowers）
- **角色**：流程骨架 + 审查门。① 用其头脑风暴技能框定范围；⑤ 用其"请求代码审查"的 severity 分级机制做三级审计，Critical 阻断进度的硬约束直接复用。
- **证据**：官方插件市场在列；宣称支持 17 个 agent harness（Claude Code / Codex / Cursor / Gemini CLI / OpenCode 等）；SessionStart hook 强制加载。
- **安装**：`/plugin install superpowers@claude-plugins-official`
- **验证 hook 是否生效**：会话内问 `Do you have superpowers?`
- **降级**：本机已装 `superpowers-zh` 技能时直接用它；都没有则用内置规划/审查能力，人工执行三级分级。

### Cognee（topoteretes/cognee）
- **角色**：记忆引擎 + codegraph 流水线。② 把代码库转成符号与依赖知识图（GraphRAG 检索，跨会话持久），③ 复用其检索。
- **证据**：~26k stars、Apache 2.0、v1.6+、有 Claude Code / Codex 官方插件。
- **安装**：`pip install cognee`，或插件 `claude plugin install cognee-memory@cognee`，或走 MCP 通道（最稳）。
- **降级**：Tree-sitter / grep 扫 import 关系 + LSP 符号索引，自建边列表。

## 2. MCP 服务器

### Cognee codegraph MCP
- **角色**：②③ 的建图与检索通道。
- **接入**：MCP 配置导入（`cognee-memory`）。

### Serena MCP
- **角色**：⑥ 符号级查找与核对（反幻觉）。
- **接入**：MCP 配置导入。
- **降级**：Cognee 检索，或 `grep -n` + 文件:行号。

### 备选（替代 Cognee 的本地代码图）
- CO CodeGraph / codebase-memory-mcp / LAIN-mcp：多个独立实现，共性是 Tree-sitter + LSP + Git 历史三件套，本地构建代码结构属性图、索引与记忆。任选其一即可。

## 3. Skill / 内置能力

| 名称 | 角色 | 状态 |
|------|------|------|
| superpowers-zh | Superpowers 的中文技能版（①⑤） | 本机 `.zcode/skills/` 已装 |
| Explore 子代理 | ③ 模块卡片 map-reduce 下钻 | ZCode 内置，无需安装 |
| archify | 流程图渲染（本技能 assets 的重渲染） | 本机 `.zcode/skills/` 已装 |

## 4. CLI 审计工具（⑤ 按技术栈选一）

| 工具 | 技术栈 | 典型用法 |
|------|--------|----------|
| ArchUnit | Java/JVM | 单测形式固化分层与禁止循环规则 |
| dependency-cruiser | JS/TS | `depcruise --validate rules.cjs src` |
| import-linter | Python | contract 配置：layered / forbidden / independence |

无法安装时：在②的边列表上自写环检测（Tarjan SCC）与分层校验脚本，覆盖同等 Critical 级检查。

## 5. 环境适配判断

- ZCode / Claude Code 环境已预置插件市场 + MCP 配置导入：Superpowers 与 Cognee 理论上可直接进。
- 首次落地顺序：先装 Superpowers 验证 hook 触发 → Cognee 走 MCP → 挑一个小仓库试跑六步 → 再决定是否把 ⑤ 独立封装成自定义技能。
- 学术注：通用 LLM 直接抽取代码关系 F1 ≈ 0.69，专门流水线 + 真实代码索引显著更高——这就是"封装过的流水线"优于"一次大提示词"的依据，也是②坚持用真图而非凭印象画图的原因。
