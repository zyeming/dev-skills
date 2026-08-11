---
name: implementation-plan-writing
description: 为具体软件需求编写、审查或更新可由 Coding Agent 执行的 Implementation Plan。可接收 Technical Design，但必须独立核验需求与范围、当前源码行为、目标行为和架构约束，再生成有顺序、可测试、可验证的实施步骤；中大型需求使用稳定的 Implementation Units。适用于实施计划、代码任务拆解或方案落地规划；不用于面向人类比较架构方案，也不直接修改代码。默认使用中文。
---

# Implementation Plan 编写

## 目标

为单个具体软件需求编写可由 Coding Agent 顺序执行、测试和验证的实施计划。

Implementation Plan 回答“已明确的目标怎样落实到当前代码”。它不是面向人类比较方案的 Technical Design，也不直接修改代码。

Technical Design 是可选但高价值的输入，不替代独立分析。即使已有 Technical Design，也必须重新理解需求和范围、根据源码验证当前行为、重建目标行为，并检查方案在当前代码中是否仍然可行。

只写影响代码落点、依赖顺序、测试覆盖、验证方式和交付风险的信息。Coding Agent 能从代码、类型系统和框架约定稳定推导出的 boilerplate 不写。

## 输入与事实来源

按问题类型使用对应来源，不给任何单一文档无限权威：

- 当前用户指令和需求决定交付目标、范围和验收标准。
- `AGENTS.md` / `CLAUDE.md` 决定允许的操作和项目规则。
- 当前源码、配置、schema 和测试决定系统现在怎样。
- 架构文档和 ADR 决定长期不能破坏的边界。
- 已确认的 Technical Design 决定本次采用的目标方案。
- Implementation Plan 决定具体代码落点、实施顺序和验证方式。

Technical Design 是输入资料，不是执行授权。它对当前行为的描述与源码冲突时，以源码为准；它与最新需求、项目规则或架构约束冲突时，明确报告，不静默覆盖。

## 工作流程

1. 读取输入和约束：
   - `AGENTS.md` / `CLAUDE.md`
   - 根级 `ARCHITECTURE.md`
   - 相关模块架构文档
   - 相关 ADR
   - 需求、issue、PRD 或用户说明
   - 用户指定的 Technical Design；没有时不要自行搜索无关方案

2. 独立整理需求和范围：
   - 目标、Non-Goals、验收标准和边界场景
   - 功能与非功能需求
   - 需要用户决策的问题和可安全采用的假设

3. 对照代码确认当前实现：
   - 当前入口、调用路径、行为差异
   - 数据所有权、状态、事务和一致性边界
   - 权限、安全、外部系统和异步流程
   - 现有测试和验收方式

4. 重建并验证目标行为：
   - 确认目标行为完整覆盖需求和验收标准。
   - 有 Technical Design 时，提取已确认决策、假设、Open Questions 和允许的实施自由度。
   - 没有 Technical Design 时，只在需求明确且不涉及重大方案选择时直接形成实施方案。

5. 检查设计适配和漂移：
   - 文件、类或入口改名属于机械漂移，修正 Touchpoints 后继续。
   - 等价 API 或既有模式变化但不改变目标语义时，记录 `Design Adaptation` 后继续。
   - 用户行为、数据所有权、状态语义、API / Event contract、权限、安全、事务或架构边界冲突时，标记 `Blocked`，退回 Technical Design 或用户决策。
   - 不因个人偏好、流行做法或轻微简洁性差异重开已确认决策。

6. 检查最小实现和演进边界：
   - 不提前实现后续版本能力。
   - 不引入当前需求不需要的模块、状态、表、队列、抽象或依赖。
   - 在不改变已确认设计的前提下，选择更少代码触点和更清晰的实施顺序。

7. 按复杂度拆分计划并读取参考文件：
   - 小需求：使用短 checklist，不强制 R-ID 或 U-ID。
   - 中大型需求：使用稳定 U-ID、依赖关系、具体测试和验证方式。
   - 只在写作或 Review 时读取 `references/implementation-plan-template.md` 和 `references/implementation-plan-review-checklist.md`。

8. 输出计划后停止，不修改代码。用 `假设：` 标记未证实信息，用 `Open Questions` 或 `Blockers` 记录不能安全推断的问题。

## Technical Design 输入处理

Technical Design 不是强制前置：

- 已确认或用户明确指定采用的设计：继承其目标方案和关键决策，但重新核验需求与源码。
- `Draft` 或状态缺失的设计：作为参考。计划依赖其中未确认的非平凡决策时只能标记 `Draft`；仍有阻塞选择时标记 `Blocked`，不能标记 `Ready`。
- 只有 Technical Design 已标记 `Approved`、当前用户明确采用，或计划完全不依赖其中未确认的决策时，计划才可以标记 `Ready`。
- 没有设计的简单需求：直接形成紧凑计划。
- 没有设计且涉及跨服务、数据所有权、重大 contract、安全、一致性或不可逆变更：不要把方案选择藏进 Unit，先转 `$technical-design-writing`。

只有以下证据可以重新打开已确认决策：

- 与最新需求或验收标准冲突。
- 依赖了被源码证明错误的事实。
- 与当前架构约束冲突且没有明确例外。
- 在当前依赖或外部 contract 下不可实现。
- 新发现数据丢失、安全、权限、兼容性或不可逆风险。

## 文档规格

模板是候选结构，不是必填清单。按需求复杂度保留必要章节，删除没有实施或验证价值的内容。

### 小需求

写清：

- Requirements / Scope
- Verified Current -> Target Behavior
- Implementation checklist
- Tests
- Verification

### 中大型需求

按需增加：

- Verified Planning Brief
- Design Intake / Drift
- Requirements trace；仅复用已有 R-ID，或在确有追踪价值时创建
- Implementation Units with stable U-IDs
- Cross-Unit Verification
- Rollout / Rollback actions
- Documentation Updates
- Open Questions / Blockers

## 结构原则

- 全局章节只保存经过核验的计划上下文、继承决策、漂移和跨 Unit 约束。
- Implementation Units 负责代码落点、依赖、测试和完成判据。
- API / Data / Event / Error / Security / Consistency 优先写在相关 Unit 下。
- 只有跨多个 Unit 的风险才放入 Cross-Cutting Concerns。
- 小需求使用普通 checklist；中大型需求使用 U-ID units 替代普通 checklist。
- 每个 feature-bearing unit 必须写具体测试场景，或说明为什么不需要测试。
- Touchpoints 只写关键入口、边界和测试位置，不列机械文件清单。
- 文件路径使用仓库相对路径，优先写路径加类、方法或模式，不使用易漂移的行号。
- 已在 Technical Design 说明的设计理由只引用，不重复。

## 计划层决策边界

Planner 可以决定：

- 真实文件、类、方法和测试位置。
- 仓库已有等价 API、惯用模式和局部算法。
- Unit 拆分、依赖顺序和验证方式。
- 不改变需求、关键决策、数据语义和安全边界的机械简化。

Planner 不得自行改变：

- 用户可见行为和验收标准。
- 数据所有权、核心数据模型或状态语义。
- API / Event contract 和跨服务责任边界。
- 权限、安全、事务、一致性、幂等及兼容策略。
- 已确认的发布和回滚策略。

有 Technical Design 时不要重复其架构图。仅当 4 个以上 Unit 存在非线性依赖，或实施顺序难以从文字理解时，增加一张从上到下的 Unit 依赖图。

## 不要写

- 大段完整代码、逐行实现、完整 API 文档或完整数据库 schema。
- 已在 Technical Design 中确认的完整方案论证和 Alternatives。
- 与当前需求无关的重构、未来扩展、平台化设计或抽象层。
- 框架 boilerplate、显而易见的 controller / service / module wiring。
- 已在架构文档里写清楚的长期原则；引用即可。
- 空章节、空 checklist、没有验收方式的模糊目标。
- 未验证的猜测；必须写成 `假设：` 或放入 `Open Questions`。
- 精确 git 命令、提交信息或大段 shell choreography。

## 输出位置

默认放在：

```text
docs/implementation-plans/{ticket-id-or-date}-{feature-name}.md
```

如果项目已有计划目录，遵守现有目录。文件名使用小写 kebab-case；有 ticket id 时优先使用 ticket id。

## 与其他 Skill 的关系

- 需求仍不清楚时使用 `$requirements-clarification`。
- 需要人类比较和确认目标方案时使用 `$technical-design-writing`。
- 本技能生成实施计划后，由 `$test-driven-development` 指导实现。
- 长期模块边界、数据所有权、事务或安全约束变化时，在 `Documentation Updates` 中安排 `$architecture-doc-writing` 或 ADR 更新。

输出后停止，不继续实现或调用其他 Skill，除非用户明确要求。
