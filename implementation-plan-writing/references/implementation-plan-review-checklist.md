# Implementation Plan Review Checklist

按需求复杂度裁剪。检查重点是计划能否由 Coding Agent 执行、测试和验证，而不是章节是否齐全。

## 独立核验

- [ ] 目标、范围、Non-Goals、验收标准和边界场景已重新理解，不只是复制输入文档。
- [ ] 当前入口、调用路径、数据与状态、权限和现有测试已根据源码核验。
- [ ] 目标行为覆盖需求，且没有提前实现未来能力。
- [ ] 架构文档、ADR 和项目规则中的相关约束已纳入计划。
- [ ] 假设、Open Questions 和 Blockers 没有被写成事实。

## Technical Design 输入

- [ ] Technical Design 的状态、已确认决策和未决问题已识别；没有设计时不虚构设计输入。
- [ ] 计划依赖未确认设计决策时没有标记 `Ready`；`Ready` 有明确批准依据，或不依赖未确认决策。
- [ ] 已确认设计只在有明确证据时重新打开。
- [ ] 源码漂移已区分为机械修正、等价 `Design Adaptation` 或 `Blocking Conflict`。
- [ ] 没有在 Implementation Unit 中静默改变用户行为、数据语义、contract、安全或架构边界。
- [ ] 没有重复 Technical Design 的完整方案论证或架构图。

## 计划结构

- [ ] 小需求使用紧凑 checklist；中大型需求使用稳定 U-ID。
- [ ] Unit 按依赖顺序组织，U-ID 不因重排而改变。
- [ ] 每个 Unit 有可观察的 Outcome、关键 Touchpoints、Approach、Tests 和 Verification。
- [ ] Dependencies 足以说明前置关系，没有把相互依赖的工作写成可并行。
- [ ] Touchpoints 使用仓库相对路径和关键类、方法或模式，不使用易漂移行号，也不列机械文件清单。
- [ ] API / Data / Event / Error / Security / Consistency 写在相关 Unit 下；只有跨 Unit 的约束才放到全局。

## 测试与追踪

- [ ] 每个 feature-bearing Unit 有具体输入、动作和期望结果，或明确说明不需要测试的原因。
- [ ] 单元测试覆盖核心规则、状态分支和失败场景。
- [ ] 集成、contract 或 e2e 测试覆盖必要的跨模块流程。
- [ ] 回归、权限、安全、幂等、并发或兼容场景在实际相关时得到覆盖。
- [ ] 每个 Verification 都是可判定的完成条件，而不是“确认正常”一类描述。
- [ ] 已有 R-ID 时能追踪到 Unit 和验证；简单需求没有为了形式强造 ID。

## 交付

- [ ] 跨 Unit 的整体验证明确。
- [ ] 发布、回滚、迁移、feature flag 或人工审批只在实际需要时写明。
- [ ] 需要更新架构文档、ADR、API docs、runbook 或运营文档时已安排对应动作。
- [ ] 高风险或不可逆动作设置了明确 Approval Gate。

## 简洁性

- [ ] 没有空章节、重复设计说明或无验收方式的模糊任务。
- [ ] 没有完整复制 API 文档、schema、源码或长期架构文档。
- [ ] 没有普通框架 wiring、逐行实现、完整代码和 shell choreography。
- [ ] Sources 只保留影响实施判断的来源，不写阅读流水账。
