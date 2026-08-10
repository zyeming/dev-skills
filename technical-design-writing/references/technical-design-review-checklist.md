# Technical Design Review Checklist

按需求风险裁剪使用。小需求不需要勾满所有项；高风险需求不能省略一致性、安全、发布和回滚。检查重点是方案是否可 review、可实现、可验收，而不是章节是否齐全。

## Requirement

- [ ] 目标清楚。
- [ ] Non-Goals / Deferred 清楚。
- [ ] 当前行为描述准确，并已对照代码。
- [ ] 目标行为可验收。
- [ ] 边界场景写清楚。
- [ ] Open Questions 没有被写成事实。
- [ ] 中大型需求有 R-ID。
- [ ] 每个 R-ID 能追踪到至少一个 Implementation Unit、Acceptance Criteria，或明确说明不需要代码变更。

## Requirement Fit / Simplicity / Evolvability

- [ ] 当前方案完整满足当前功能需求。
- [ ] 当前方案覆盖当前明确或隐含的非功能需求。
- [ ] 没有提前实现后续版本能力。
- [ ] 没有不必要的模块、状态、表、队列、抽象或外部依赖。
- [ ] 已指出可以删除、合并或后置的设计。
- [ ] 当前边界清晰，未来可以增量演进，不需要推翻核心模型。

## Technical Shape

- [ ] 有关键技术取舍时写了 Key Technical Decisions。
- [ ] KTD 写的是会约束实现的选择，不是普通任务列表。
- [ ] 复杂流程有 High-Level Technical Design。
- [ ] High-Level Technical Design 不是文字重复或装饰图。
- [ ] AI Coding 能稳定推导的 boilerplate 没有写进方案。

## Architecture

- [ ] 没有破坏模块边界。
- [ ] 没有破坏数据所有权。
- [ ] 没有绕过既有 Application Service、状态机或 owner service。
- [ ] 没有把业务逻辑放进 Controller、Job、Adapter、Repository 或全局 utility。
- [ ] 如果改变长期架构，已标注需要更新架构文档或 ADR。
- [ ] 如果方案违反既有约束，已说明例外原因和后续文档动作。

## Implementation Units

- [ ] 中大型需求使用 U-ID units，而不是普通 checklist。
- [ ] U-ID 不因重排而重编号。
- [ ] 每个 Unit 有 Outcome、Requirements、Touchpoints 和必要的 Design Notes。
- [ ] API / Data / Event / Error / Security / Consistency 写在相关 Unit 下，或在 Cross-Cutting Concerns 中解释为什么是跨 Unit 风险。
- [ ] 每个 feature-bearing Unit 有具体 Tests，或说明为什么不需要。
- [ ] Touchpoints 只列关键入口、边界、测试位置和模式参考，没有机械文件清单。

## Cross-Cutting Concerns

- [ ] 只有跨多个 Unit 的风险才写在全局。
- [ ] 事务边界、一致性模型、幂等策略、并发场景写在相关 Unit 或 Cross-Cutting Concerns 中。
- [ ] 权限、安全、租户隔离、敏感数据处理写在相关 Unit 或 Cross-Cutting Concerns 中。
- [ ] 外部系统失败、超时、重试、补偿或异步重复消费处理清楚。

## Testing

- [ ] 单元测试覆盖核心业务规则和状态分支。
- [ ] 集成或 e2e 测试覆盖跨模块流程。
- [ ] 失败场景有测试。
- [ ] 幂等和并发场景有测试，或说明为什么当前需求不涉及。
- [ ] 回归场景明确。
- [ ] 验收标准可以被测试或人工验证。

## Delivery

- [ ] 发布计划清楚，或说明为什么不需要。
- [ ] 回滚计划清楚，或说明为什么代码回滚足够。
- [ ] 数据是否可逆清楚。
- [ ] 风险和缓解措施清楚。
- [ ] 需要 feature flag、灰度或运维 runbook 时已写明。

## Documentation Updates

- [ ] 已判断是否需要更新根级 `ARCHITECTURE.md`。
- [ ] 已判断是否需要更新模块架构文档。
- [ ] 已判断是否需要新增或更新 ADR。
- [ ] 已判断是否需要更新 API docs、runbook 或运营文档。
- [ ] 不需要更新的文档有原因，不只是空 checkbox。

## Conciseness

- [ ] 没有空章节。
- [ ] 没有重复写同一设计点。
- [ ] 没有完整复制 API 文档、schema 或架构文档。
- [ ] 没有把普通框架 wiring 当成方案内容。
- [ ] Sources And Research 只保留影响方案判断的来源。
