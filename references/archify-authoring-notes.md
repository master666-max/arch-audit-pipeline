# archify 实操要点（本流程图的已验证修法）

本技能 assets 里的流程图用 archify workflow v2（可读布局契约）落地。以下是在真实校验循环中踩到并验证过的坑，重渲染改图前先读。

## 命令序列

```bash
cd <archify技能目录>
node bin/archify.mjs validate workflow <spec.json> --quality showcase --json   # 迭代期，每次改动后
node bin/archify.mjs deliver workflow <spec.json> <out.html> --quality showcase --json  # 最终验收，通过后冻结
node bin/archify.mjs visual-check <out.html> --json                            # 浏览器证据（包含性+可读性）
```

## 布局与路由（本次验证过的结论）

1. **泳道顺序即布线自由度**。异常/阻断泳道放最下方时，主流程到阻断节点的垂直 drop 通道必须畅通——若中间泳道在同列有节点，drop 会被判 `workflow/route-preset-conflict`。修法：把"依赖封装"泳道移到主流程**上方**，让 drop 通道下方无遮挡。
2. **短垂直边放不下标签**。两泳道间隙约 58px 时，CJK 标签（掩码宽 >50px）在 drop 边上必然失败。若语义已被终点节点完全蕴含（如 `audit→blocked` 的"发现 Critical" vs 终点标签"Critical 阻断"），按契约直接省略标签是合法的语义取舍，不是删信息。
3. **return 回路三连败的教训**：同列反向边依次试 `return-left`（通道夹在两列窄缝、削到邻节点）→ `outside-right`（外绕水平段穿过第三方的节点盒）→ `up-channel`（塌缩成与错误边共线重叠）全失败后，**去掉 route preset 用 auto + 钉住 fromSide/toSide** 让编译器解算端口散布，一次通过。平行双箭头（下行实线 error + 上行虚线 return）是可读的成熟样式。
4. **副标签宽度按 CJK 算**：全角字符=2 单位，约 12 单位/84px 上限。`Critical / Major / Minor`（87px）超宽，缩为"严重度分级"并把完整分级写进卡片/文档。诊断信息会给出精确像素差，按它改。

## 视口包含性（visual-check 的硬门）

- 校验视口 1440×900 / 1600×1000 / 1920×1080 / 2048×1320（大屏另加 2048×1320），要求 `scrollHeight ≤ innerHeight` 且投影后节点文字 ≥6px。
- **结论卡文字长度直接决定页面高度**：三张卡、每条 30+ 全角字符时 1440×900 溢出 ~79px；缩短卡条只救了 1920+，**删掉与图内泳道重复的整卡**才压进 900px。改图时优先砍与节点/泳道重复的卡片，而不是缩字号（缩字号是被禁止的作弊修法）。
- 修复顺序必须是：删冗余内容 → 压缩间距 →（最后才是）缩节点/标签。`overflow:hidden`、内滚、拉伸 SVG 高度都算伪造通过。
- 11px 级的微小溢出若删内容后仍不过，查页面固定 chrome（工具条/页头/图例的固有高度），别再死磕卡片换行。

## 其他

- 新 workflow 一律 `schema_version: 2`（col 为 0..5 逻辑秩，几何由编译器推导），不要写死坐标。
- `meta.locale: "zh-CN"` 控制查看器 UI 语言；标题外的品牌名/命令保留英文。
- `semanticChecks` 里把零入度节点（流程起点+全部依赖节点）列入 `allowedRoots`、终点列入 `allowedTerminals`，可在布局前拦住结构错误。
- 感知复核与机器证据分开汇报：`deliver` 证明确定性检查，`visual-check` 证明真实浏览器行为，两者都不代替人看一眼截图。
