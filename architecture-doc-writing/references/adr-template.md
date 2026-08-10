# ADR 模板

ADR 用于记录重要架构决策及其原因，不用于记录日常实现细节、API 字段、事件 payload 或模块百科。

创建或更新 ADR 的典型场景：

- 模块边界变化。
- 数据所有权变化。
- 事务或一致性模型变化。
- 引入新的事件驱动流程。
- 引入重大依赖。
- 做出安全敏感决策。
- 接受带有明确代价的捷径。
- 故意违反既有架构规则。

## 结构

按决策复杂度保留必要章节。不要为了填满模板而扩写背景或备选方案。

# ADR {number}: {Decision Title}

## Status

Proposed | Accepted | Superseded | Rejected

## Context

描述问题、当前情况、约束和风险。

包括：

- 当前系统是什么状态。
- 为什么现在需要决策。
- 必须遵守哪些约束。
- 有哪些风险和取舍。

## Decision

清楚陈述决策。

示例：

我们将使用 Outbox Pattern 发布必须与数据库变更保持一致的 domain events。

## Rationale

说明为什么选择该方案。

- ...

## Consequences

正面影响：

- ...

负面影响：

- ...

运维影响：

- ...

## Alternatives Considered

### 方案一：...

优点：

- ...

缺点：

- ...

### 方案二：...

优点：

- ...

缺点：

- ...

## Follow-up Work

- ...
