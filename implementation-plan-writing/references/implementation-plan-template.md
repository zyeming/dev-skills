# Implementation Plan 模板

模板是候选结构，不是必填清单。计划必须足以让 Coding Agent 实施和验证，但不重复 Technical Design 的方案论证。

## 小需求最小结构

适用于单模块、低风险、无复杂状态或 contract 变化的改动。不强制 R-ID 或 U-ID。

```md
# Implementation Plan: {Feature Name}

Status: Draft / Ready / Blocked

## Requirements / Scope

## Verified Current -> Target Behavior

## Implementation Checklist

## Tests

## Verification
```

## 推荐结构

中等和复杂需求优先使用下面结构。全局章节保存经过核验的实施上下文、继承决策和跨 Unit 约束；具体改动、测试和完成判据写入相关 Implementation Unit。

```md
# Implementation Plan: {Feature Name}

## 1. Metadata

| Field                | Value                   |
| -------------------- | ----------------------- |
| Requirement / Ticket | ...                     |
| Status               | Draft / Ready / Blocked |
| Technical Design     | path / None；状态与采用依据 |
| Observed Revision    | branch / commit / date  |

## 2. Verified Planning Brief

用短列表记录重新核验后的计划上下文，不复制需求或 Technical Design 原文。

### Scope Boundaries

#### In Scope

- ...

#### Non-Goals / Deferred

- ...

### Current -> Target Behavior

当前行为：

- ...

目标行为：

- ...

边界行为：

- ...

### Constraints / Assumptions

- ...

## 3. Design Intake

仅有 Technical Design 时保留：

- Adopted Decisions: 必须继承的关键设计决定。
- Design Adaptations: 因等价 API 或既有模式变化而作的局部适配；机械改名只修正 Touchpoints。
- Blocking Conflicts: 会改变目标语义或安全边界、必须退回决策的问题。

不要重写设计理由和架构说明。

计划依赖未确认的非平凡设计决策时，`Status` 只能为 `Draft`；仍有阻塞选择时为 `Blocked`。只有设计已批准、用户明确采用，或计划不依赖未确认决策时，才可标记 `Ready`。

## 4. Requirements

- R1. ...
- R2. ...

## 5. Implementation Decisions

只写 Plan 有权决定的局部实现选择，例如现有模式复用、文件落点和 Unit 顺序。没有非显而易见选择时删除本节。

## 6. Implementation Strategy

用 1—2 段说明实施顺序和依赖。仅当 4 个以上 Unit 存在非线性依赖时增加一张 Unit 依赖图；不要复制 Technical Design 的架构图。

## 7. Implementation Units

### U1. {Unit Name}

- Outcome:
- Requirements:
- Dependencies:
- Touchpoints:
- Approach:
- Contract / Data / Event Changes:
- Failure / Security / Consistency Notes:
- Tests:
- Verification:

### U2. {Unit Name}

- Outcome:
- Requirements:
- Dependencies:
- Touchpoints:
- Approach:
- Tests:
- Verification:

## 8. Cross-Unit Concerns

仅跨多个 Unit 的事务、一致性、权限、安全、兼容或发布约束在这里汇总；否则删除本节。

## 9. Cross-Unit Verification

- Unit / component tests: ...
- Integration / contract tests: ...
- Regression tests: ...
- Manual verification: 仅在人工验收确有必要时保留。

## 10. Rollout / Rollback

仅发布风险、兼容性、迁移或外部系统变更时保留。

## 11. Requirement Coverage

仅已有 R-ID 或需求较复杂时保留。

- R1 -> U1, U3 -> verification ...

## 12. Documentation Updates

Required:

- ...

Not required:

- `ARCHITECTURE.md`: ...
- ADR: ...

## 13. Open Questions / Blockers

- ...

## 14. Sources

- `path/to/file.ts`: ...
- `path/to/technical-design.md`: inherited decisions ...
```

## Implementation Unit 字段规则

字段按需保留。没有真实内容的字段直接删除，不写 `None`。

- **Outcome:** 这个 Unit 完成后的可观察结果。
- **Requirements:** 引用 R-ID，例如 `R1, R3`。如果某个 R-ID 不需要代码变更，明确写在 Requirements、Requirement Coverage 或 Verification 中。
- **Dependencies:** 引用其他 U-ID；没有依赖时可省略。
- **Touchpoints:** 只写关键入口、边界、测试位置和模式参考，不列机械文件清单。
- **Approach:** 写本 Unit 的修改顺序、复用点和非显而易见的局部实现判断；不要重复 Technical Design 的方案理由。
- **Contract / Data / Event Changes:** 只写本 Unit 涉及的 API、DTO、数据、事件、队列、回调或 provider contract 差异。
- **Failure / Security / Consistency Notes:** 只写本 Unit 涉及的失败处理、权限、安全、事务、一致性、幂等和并发规则。
- **Tests:** 写具体输入、动作、期望结果；每个 feature-bearing unit 必须有测试场景或说明为什么不需要测试。
- **Verification:** 写完成判据，不写冗长命令脚本。

## Design Intake 写法

Technical Design 是可选输入，不能替代独立核验。

- 已确认设计：继承目标方案，重新核验需求、当前源码和可实现性。
- Draft 设计：区分已明确决定和未决问题；未决问题影响实施时保持 `Draft` 或 `Blocked`。
- 机械漂移：修正 Touchpoints 后继续，不必记录成设计变化。
- 等价适配：记录 `Design Adaptation` 后继续。
- 语义、安全、数据所有权、contract 或架构边界冲突：记录 `Blocking Conflict`，不把新设计藏进 Unit。

## 跨 Unit 内容使用规则

不要把所有风险都放到全局。优先把具体风险写在相关 Unit 下。

只有约束确实跨多个 Unit 时，才放入全局说明，例如：

- 多个 Unit 共享同一事务或一致性模型。
- 多个入口共享权限、安全或租户隔离规则。
- 多个 provider / adapter 共享 fallback 或错误映射。
- 发布、回滚、兼容性会影响多个模块。

## Tests 与 Verification 写法

具体 test scenarios 写在每个 Unit 下；Cross-Unit Verification 只覆盖跨层契约、回归和整体完成判据。

可保留的全局测试汇总：

- Unit tests: 覆盖哪些核心规则。
- Integration / e2e tests: 覆盖哪些跨层契约。
- Regression tests: 哪些旧行为必须保持。
- Manual verification: 只有人工验收必要时才写。

## Sources 写法

只写会影响方案判断的来源，不写阅读流水账。

好的来源包括：

- 约束本方案边界的架构文档。
- 要复用或保持兼容的现有实现。
- 影响 provider、框架、协议或外部 contract 的官方文档。
- 需要继承的 Technical Design、历史方案、ADR 或需求文档。
