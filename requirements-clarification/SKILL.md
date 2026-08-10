---
name: requirements-clarification
description: 澄清模糊、不完整或存在多种解读的用户需求；在实现、设计、写作、调研或自动化任务开始前，将需求整理为可确认的 Summary、Needs Confirmation、Confirmed Requirements、Constraints and Assumptions、Out of Scope 和 Sources。Use when the user asks to clarify requirements, refine scope, confirm assumptions, prepare requirements, or when key goal/scope/acceptance/safety details are missing.
---

# 需求澄清

## 目标

将用户提出的需求整理为可确认的需求说明；必要时提出澄清问题。只澄清需求，不进入实现、技术方案或执行步骤。

## 适用边界

用于澄清需求，不用于实现、技术方案或执行步骤。若用户明确要求直接执行，且缺口不影响目标、验收、安全或范围边界，则不要停下来澄清。

## 工作流程

1. 盘点需求是否说清楚：
   - objective：要做什么、保留什么
   - done：验收标准、例子、边界场景
   - scope：哪些对象、模块、用户、数据在内或在外
   - constraints：兼容性、性能、风格、依赖、时限
   - environment：运行环境、版本、工具链
   - safety：数据、权限、回滚、可逆性、风险

2. 先用已有上下文消除疑问。能从用户输入、附件、历史上下文、文档、配置或项目说明中确认的，不再提问。

3. 将结论分成：
   - `Confirmed Requirements`：已确认的需求
   - `Constraints and Assumptions`：已确认约束和默认假设
   - `Needs Confirmation`：需要用户拍板的问题
   - `Out of Scope`：明确不做或应排除的范围

## Needs Confirmation

优先一次性列出需要用户拍板的问题，减少来回确认；简单场景只问最关键的问题。

- `Need to know`：未确认会影响目标、验收、安全或范围边界。
- `Nice to know`：可选；仅当非阻塞选择会明显影响验收或用户可见行为时才写。
- 低风险默认项写入 `Constraints and Assumptions`，不提问。

每个问题应包含：

- 简短问句
- 2-4 个互斥选项
- 推荐默认
- `Not sure - use default`

块格式参考 `references/open-questions-template.md`。

## 输出

如果调用上下文指定了输出路径、标题、章节或格式，严格遵守。否则默认用 Markdown 输出：

```text
# Requirements Clarification

## Summary
## Needs Confirmation
## Confirmed Requirements
## Constraints and Assumptions
## Out of Scope
## Sources
```

没有内容的章节可以省略；调用上下文要求必填的除外。输出后停下来等用户确认，不继续实现或技术方案。

`Sources` 只在存在附件、文档、评论、外部材料或调用上下文要求来源引用时输出。

## 反模式

- 不要为低风险默认项单独提问。
- 不要问能从上下文确认的问题。
- 不要把实现细节、文件路径、类名、接口签名作为需求问题选项。
- 不要把临时执行限制写成需求本身。
- 不要用低权威来源覆盖高权威来源；有冲突就列为 `Needs Confirmation`。
- 不要在需求澄清阶段继续实现或写技术方案。
