# Needs Confirmation 与 Constraints and Assumptions 模板

本模板规定 `## Needs Confirmation` 与 `## Constraints and Assumptions` 的标准块格式。
目的是让用户尽量少来回，也能完成需要拍板的澄清。

## 顶部回复说明

在 `## Needs Confirmation` 段第一行写明回复方式，例如：

> 回复 `defaults` 全部采用推荐项；或用 `1a 2b 3c` 自由组合；未列出的项按 Constraints and Assumptions 处理。

## Need to know（阻塞执行）

未答清楚会阻塞执行的问题。每条给：编号、简短问句、2–4 个互斥选项、推荐默认、兜底项。

```text
### Need to know

1) <简短问题>？
   a) **<推荐默认>（默认/推荐）**
   b) <备选项>
   c) <备选项>
   d) Not sure - use default

2) <简短问题>？
   a) **<推荐默认>（默认/推荐）**
   b) <备选项>
   c) Not sure - use default
```

## Nice to know（可选）

仅当非阻塞选择会明显影响验收或用户可见行为时才写；不要为了补全模板而创建。格式同上，编号继续往下排。

```text
### Nice to know

3) <非阻塞但影响验收或用户可见行为的问题>？
   a) **<推荐默认>（默认/推荐）**
   b) <备选项>
   c) Not sure - use default
```

## Constraints and Assumptions（与 Needs Confirmation 配合）

列出已确认约束和"用户不回答时将默认采用的取值"，逐条独立成行，方便用户复核或直接否决。

```text
## Constraints and Assumptions

- <约束或默认假设 1>。
- <约束或默认假设 2>。
```

## 写法要求

- `Nice to know` 可省略；没有必要时只保留 `Need to know`。
- 编号在整份输出内连续，不要按分组重新计数。
- 选项使用 a) b) c) d)；推荐项加粗，并在末尾追加 `(默认/推荐)`。
- 每个问题都必须包含 `Not sure - use default` 兜底项。
- 选项之间互斥；如确需多选，问句中明确写"可多选"，回复格式改为 `1ab 2c`。
- 不要在选项里写文件路径、类名、函数签名或代码片段。
- 一个问题只问一件事；复合问题必须拆开。
- 低风险且有合理默认的问题写入 `Constraints and Assumptions`，不要进入 `Needs Confirmation`。
