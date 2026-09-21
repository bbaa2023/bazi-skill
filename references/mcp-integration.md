# Bazi MCP Integration

## 目的

本 Skill 使用 OpenCode 已连接的 `bazi` MCP 作为主排盘计算引擎。

- MCP：`@lhk714/bazi-mcp`
- 主工具：`calculate_bazi`
- 辅助工具：`lookup_location`
- Skill：负责命理规则、证据组织和解释
- LLM：负责理解用户问题、调用工具、组织输出

不要让 LLM 自己计算四柱，也不要让本 Skill 的旧版 `scripts/pai_pan.py` 覆盖 MCP 结果。

## 推荐调用流程

1. 收集并确认出生日期、时间、性别、地点。
2. 如果地点名称可能有歧义，先调用 `lookup_location`。
3. 调用 `calculate_bazi`。
4. 保留原始计算结果和 diagnostics。
5. 根据 `strengthFactors`、四柱、十神、藏干、刑冲合会、大运等事实进行命理分析。
6. 分析时明确流派和规则，不把计算事实和解释性判断混为一谈。

## 关键参数

`calculate_bazi` 支持 `solarDate` / `lunarDate`、`clockTime` / `shichen` / `timeUnknown`、`gender`、`place`、`longitude`、`timezone`、`solarTime` 和 `sect`。

默认优先使用 `solarTime: true`。只有用户明确要求时才切换到 `mean` 或 `off`。

## 结果解释边界

MCP 的 `strengthFactors` 是零权重证据台账，不是身强身弱、格局或用神的最终判定。必须由 Skill 按明确流派进行判断。

MCP 返回的大运年龄使用 nominal age（虚岁）标签。输出时应注明口径，避免与周岁混淆。

## 失败回退

只有在 MCP 不可用时才运行 `scripts/pai_pan.py`。回退结果必须明确标记为离线回退，不得伪装成 MCP 结果。
