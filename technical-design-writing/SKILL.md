---
name: technical-design-writing
description: 编写、审查或更新 Technical Design / Implementation Plan 时使用。适用于具体需求实现前的技术方案，包括目标、非目标、当前/目标行为、需求追踪、关键技术决策、High-Level Technical Design、Implementation Units、数据/API/事件变更、事务一致性、幂等、并发、权限安全、错误处理、测试方案、发布回滚、风险、验收标准和文档更新项。输出默认使用中文。
---

# 技术实现方案编写

## 目标

使用本技能为单个具体需求编写可 review、可实现、可验收的技术实现方案。

技术实现方案回答“这次应该怎么安全地改”。它不是 PRD、长期架构文档、ADR 或代码生成提示词。

技术方案必须在满足当前功能和非功能需求的前提下，选择最简单、最直接、可长期演进的实现路径；Review 时不仅检查缺漏和风险，也要检查是否过度设计。

技术方案只写 load-bearing information：会影响设计判断、实现边界、测试覆盖、上线风险的内容。AI Coding 可以从代码、类型系统、框架约定稳定推导出的 boilerplate 不写。

- 架构文档告诉 AI 不能乱来。
- 技术实现方案告诉 AI 这次怎么做。
- ADR 记录为什么做出长期架构决策。

## 工作流程

1. 先读现有约束：
   - `AGENTS.md` / `CLAUDE.md`
   - 根级 `ARCHITECTURE.md`
   - 相关模块架构文档
   - 相关 ADR
   - 需求、issue、PRD 或用户说明

2. 对照代码确认当前实现：
   - 当前入口、调用路径、行为差异
   - 数据所有权、状态、事务和一致性边界
   - 权限、安全、外部系统和异步流程
   - 现有测试和验收方式

3. 检查需求适配、简化空间和演进边界：
   - 当前方案是否完整满足当前功能和非功能需求。
   - 是否引入了当前需求不需要的模块、状态、表、队列、抽象或外部依赖。
   - 是否把后续版本能力提前做进当前方案。
   - 是否存在更少状态、更少模块、更少协作面的实现路径。
   - 简化后是否仍不牺牲当前需求。
   - 当前边界是否清晰，未来能否增量演进而不是推翻重做。

4. 按复杂度选择文档规格：
   - 小需求：短写，不引入 U-ID 和复杂章节。
   - 中等需求：写清 R-ID、KTD、Implementation Units 和主要风险。
   - 复杂 / 高风险需求：按需加入 High-Level Technical Design、一致性、幂等、并发、安全、失败处理、发布回滚。

5. 只在需要时读取参考文件：
   - 标准模板：`references/technical-design-template.md`
   - Review 清单：`references/technical-design-review-checklist.md`

6. 写作时保持可 review、可实现、可验收：
   - 足够具体，让后继 AI Coding agent 能实现。
   - 不写逐行代码或大段完整代码。
   - 不重复完整 API 文档、数据库 schema 或长期架构原则。
   - 用 `假设：` 标记无法从代码或需求证明的信息。
   - 用 `Open Questions` 记录需要用户或维护者决策的问题。

## 文档规格

模板是候选结构，不是必填清单。按需求复杂度保留必要章节，删除没有真实风险、实现价值或验收价值的内容。

### 小需求

适用于单模块、低风险、无复杂状态或外部系统的改动。

写：

- Summary
- Scope
- Current -> Target Behavior
- Implementation Plan
- Test Plan
- Acceptance Criteria

### 中等需求

适用于跨模块、涉及 API / Data / Event 变化，或存在关键技术取舍的改动。

在小需求基础上增加：

- Requirements with R-IDs
- Key Technical Decisions
- Architecture Constraints
- Implementation Units with U-IDs
- Risks / Alternatives
- Sources And Research

### 复杂 / 高风险需求

适用于状态、异步流程、外部系统、支付、权限、账户、资金、多租户、数据迁移、并发一致性或复杂 provider 适配。

在中等需求基础上按需增加：

- High-Level Technical Design
- State Machine
- Transaction and Consistency
- Idempotency
- Concurrency
- Security and Authorization
- Failure Handling
- Rollout / Rollback
- Acceptance Examples

## 结构原则

- 全局章节负责方向、范围、需求、关键决策和必要图示。
- Implementation Units 负责具体怎么改。
- API / Data / Event / Error / Security / Consistency 优先写在相关 Unit 下。
- 只有跨多个 Unit 的风险才放入 Cross-Cutting Concerns。
- 小需求使用普通 checklist；中大型需求使用 U-ID units 替代普通 checklist。
- 每个 feature-bearing unit 必须写具体测试场景，或说明为什么不需要测试。
- Touchpoints 只写关键入口、边界和测试位置，不列机械文件清单。

## High-Level Technical Design 使用规则

High-Level Technical Design 是复杂方案的阅读加速器，不是固定装饰章节。

仅当纯文字不足以让 reviewer 快速理解方案形状时保留，例如：

- 跨多个模块的数据流、调用流或 ownership 边界。
- 异步流程、回调、队列、轮询、状态收敛。
- 状态机、生命周期或事务边界变化。
- 多 provider / adapter / model registry 分发逻辑。
- 权限、安全、幂等、并发或失败降级规则需要图示。
- 复杂分支、fallback 或兼容策略。

如果方案可以用 1-2 段文字讲清楚，不写本节。
如果图只是重复文字，不写本节。
优先一张图，最多两张图。

## 不要写

- 大段完整代码、逐行实现、完整 API 文档或完整数据库 schema；只写本次变化。
- 与当前需求无关的重构、未来扩展、平台化设计或抽象层。
- 框架 boilerplate、显而易见的 controller / service / module wiring。
- 已在架构文档里写清楚的长期原则；引用即可。
- 空章节、空 checklist、没有验收方式的模糊目标。
- 未验证的猜测；必须写成 `假设：` 或放入 `Open Questions`。

## 输出位置

默认放在：

```text
docs/technical-designs/{ticket-id-or-date}-{feature-name}.md
```

如果项目已有技术方案目录，遵守现有目录。文件名使用小写 kebab-case；有 ticket id 时优先使用 ticket id。

## 与架构文档和 ADR 的关系

- 技术方案记录单个需求的短中期实现蓝图。
- 架构文档记录长期稳定约束。
- ADR 记录重要架构决策及其原因。

如果技术方案改变模块边界、数据所有权、事务模型、一致性模型、跨模块事件流程或长期安全约束，必须在 `Documentation Updates` 中标记需要更新架构文档或新增 ADR。
