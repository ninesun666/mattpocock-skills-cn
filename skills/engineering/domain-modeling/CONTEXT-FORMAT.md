# CONTEXT.md Format

## Structure

```md
# {上下文名称}

{一两句话描述这个上下文是什么、为什么存在。}

## 术语

**Order(订单)**:
{一两句话描述该术语}
_避免使用_: Purchase, transaction

**Invoice(发票)**:
交付后向客户发出的付款请求。
_避免使用_: Bill, payment request

**Customer(客户)**:
下单的个人或组织。
_避免使用_: Client, buyer, account
```

## Rules

- **Be opinionated.** When multiple words exist for the same concept, pick the best one and list the others under `_Avoid_`.
- **Keep definitions tight.** One or two sentences max. Define what it IS, not what it does.
- **Only include terms specific to this project's context.** General programming concepts (timeouts, error types, utility patterns) don't belong even if the project uses them extensively. Before adding a term, ask: is this a concept unique to this context, or a general programming concept? Only the former belongs.
- **Group terms under subheadings** when natural clusters emerge. If all terms belong to a single cohesive area, a flat list is fine.

## Single vs multi-context repos

**Single context (most repos):** One `CONTEXT.md` at the repo root.

**Multiple contexts:** A `CONTEXT-MAP.md` at the repo root lists the contexts, where they live, and how they relate to each other:

```md
# 上下文地图

## 上下文

- [Ordering](./src/ordering/CONTEXT.md): 接收并跟踪客户订单
- [Billing](./src/billing/CONTEXT.md): 生成发票并处理付款
- [Fulfillment](./src/fulfillment/CONTEXT.md): 管理仓库拣货和发货

## 关系

- **Ordering → Fulfillment**: Ordering 发出 `OrderPlaced` 事件;Fulfillment 消费它启动拣货
- **Fulfillment → Billing**: Fulfillment 发出 `ShipmentDispatched` 事件;Billing 消费它生成发票
- **Ordering ↔ Billing**: `CustomerId` 和 `Money` 的共享类型
```

The skill infers which structure applies:

- If `CONTEXT-MAP.md` exists, read it to find contexts
- If only a root `CONTEXT.md` exists, single context
- If neither exists, create a root `CONTEXT.md` lazily when the first term is resolved

When multiple contexts exist, infer which one the current topic relates to. If unclear, ask.
