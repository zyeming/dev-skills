# Technical Design 模板

模板是候选结构，不是必填清单。按需求复杂度保留必要章节，删除没有真实风险、实现价值或验收价值的内容。

## 小需求最小结构

适用于单模块、低风险、无复杂状态或外部系统的改动。

```md
# Technical Design: {Feature Name}

## Summary

## Scope

## Current -> Target Behavior

## Implementation Plan

## Test Plan

## Acceptance Criteria
```

## 推荐结构

中等和复杂需求优先使用下面结构。全局章节只负责方向、边界、需求、关键决策和必要图示；具体 API / Data / Event / Error / Security / Consistency 优先写入相关 Implementation Unit。

```md
# Technical Design: {Feature Name}

## 1. Metadata

| Field                     | Value                                      |
| ------------------------- | ------------------------------------------ |
| Requirement / Ticket      | ...                                        |
| Status                    | Draft / In Review / Approved / Implemented |
| Date                      | YYYY-MM-DD                                 |
| Related Modules           | ...                                        |
| Related Architecture Docs | ...                                        |
| Related ADRs              | ...                                        |

## 2. Summary

用 3-5 句话说明本次需求要做什么、为什么做、总体方案是什么。

## 3. Scope Boundaries

### In Scope

- ...

### Non-Goals / Deferred

- ...

## 4. Current -> Target Behavior

当前行为：

- ...

目标行为：

- ...

边界行为：

- ...

## 5. Requirements

- R1. ...
- R2. ...

## 6. Key Technical Decisions

- KTD1. **...：** ...
- KTD2. **...：** ...

## 7. High-Level Technical Design

仅复杂流程保留。用于说明跨模块调用、异步流程、状态机、provider 分发、安全/事务/并发边界或复杂 fallback。

## 8. Implementation Units

### U1. {Unit Name}

- Outcome:
- Requirements:
- Dependencies:
- Touchpoints:
- Design Notes:
- Contract / Data / Event Changes:
- Failure / Security / Consistency Notes:
- Tests:
- Verification:

### U2. {Unit Name}

- Outcome:
- Requirements:
- Dependencies:
- Touchpoints:
- Design Notes:
- Tests:

## 9. Cross-Cutting Concerns

仅跨多个 Unit 的风险保留，例如共享一致性模型、权限边界、幂等策略、全局兼容性、统一错误处理。

## 10. Rollout / Rollback

仅发布风险、兼容性、迁移或外部系统变更时保留。

## 11. Acceptance Criteria / Acceptance Examples

Acceptance Criteria:

- [ ] ...

Acceptance Examples:

- AE1. Given ..., when ..., then ...

## 12. Documentation Updates

Required:

- ...

Not required:

- `ARCHITECTURE.md`: ...
- ADR: ...

## 13. Open Questions

- ...

## 14. Sources And Research

- `path/to/file.ts`: ...
- External docs: ...
```

## Implementation Unit 字段规则

字段按需保留。没有真实内容的字段直接删除，不写 `None`。

- **Outcome:** 这个 Unit 完成后的可观察结果。
- **Requirements:** 引用 R-ID，例如 `R1, R3`。如果某个 R-ID 不需要代码变更，明确写在全局 Requirements 或 Acceptance Criteria 中。
- **Dependencies:** 引用其他 U-ID；没有依赖时可省略。
- **Touchpoints:** 只写关键入口、边界、测试位置和模式参考，不列机械文件清单。
- **Design Notes:** 写非显而易见的设计判断、边界、复用点和禁止事项。
- **Contract / Data / Event Changes:** 只写本 Unit 涉及的 API、DTO、数据、事件、队列、回调或 provider contract 差异。
- **Failure / Security / Consistency Notes:** 只写本 Unit 涉及的失败处理、权限、安全、事务、一致性、幂等和并发规则。
- **Tests:** 写具体输入、动作、期望结果；每个 feature-bearing unit 必须有测试场景或说明为什么不需要测试。
- **Verification:** 写完成判据，不写冗长命令脚本。

## High-Level Technical Design 使用规则

High-Level Technical Design 是复杂方案的阅读加速器，不是固定装饰章节。

保留本节的典型情况：

- 跨多个模块的数据流、调用流或 ownership 边界。
- 异步流程、回调、队列、轮询、状态收敛。
- 状态机、生命周期或事务边界变化。
- 多 provider / adapter / model registry 分发逻辑。
- 权限、安全、幂等、并发或失败降级规则需要图示。
- 复杂分支、fallback 或兼容策略。

省略本节的情况：

- 方案可以用 1-2 段文字讲清楚。
- 图只是重复文字。
- 单 service 内部逻辑、简单 CRUD、字段校验或局部重构。

优先一张图，最多两张图。

## Cross-Cutting Concerns 使用规则

不要把所有风险都放到全局。优先把具体风险写在相关 Unit 下。

只有风险跨多个 Unit 时，才放入 Cross-Cutting Concerns，例如：

- 多个 Unit 共享同一事务或一致性模型。
- 多个入口共享权限、安全或租户隔离规则。
- 多个 provider / adapter 共享 fallback 或错误映射。
- 发布、回滚、兼容性会影响多个模块。

## Test Plan 写法

如果使用 Implementation Units，具体 test scenarios 写在每个 Unit 下；全局 Test Plan 只做汇总，或直接省略。

可保留的全局测试汇总：

- Unit tests: 覆盖哪些核心规则。
- Integration / e2e tests: 覆盖哪些跨层契约。
- Regression tests: 哪些旧行为必须保持。
- Manual verification: 只有人工验收必要时才写。

## Sources And Research 写法

只写会影响方案判断的来源，不写阅读流水账。

好的来源包括：

- 约束本方案边界的架构文档。
- 要复用或保持兼容的现有实现。
- 影响 provider、框架、协议或外部 contract 的官方文档。
- 解释关键取舍的历史方案、ADR 或需求文档。
