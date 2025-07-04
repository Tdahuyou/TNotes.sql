# [0065. MySQL 非法值处理机制 - 零值转换与严格模式](https://github.com/Tdahuyou/TNotes.sql/tree/main/notes/0065.%20MySQL%20%E9%9D%9E%E6%B3%95%E5%80%BC%E5%A4%84%E7%90%86%E6%9C%BA%E5%88%B6%20-%20%E9%9B%B6%E5%80%BC%E8%BD%AC%E6%8D%A2%E4%B8%8E%E4%B8%A5%E6%A0%BC%E6%A8%A1%E5%BC%8F)

<!-- region:toc -->

- [1. 📝 概述](#1--概述)

<!-- endregion:toc -->

## 1. 📝 概述

- 思考
  - 🤔 当往 DATE 类型中插入一个非法的日期值 `'2023-02-30'` 时会发生什么？
  - 🤔 为什么在插入非法值的时候，有时会报错，有时会静默转换？
- 每一个类型都有合法的取值范围，当指定确实不合法的值时系统将“零”值插入到数据库中。
  - 这表示当你向 MySQL 表中插入一个非法值（超出该数据类型的合法取值范围）时，MySQL 会自动将其转换为对应数据类型的 **“零值”**（zero value），而不是报错并中断操作。
  - 例如：
    - 对于 `DATE` 类型，非法日期如 `'2023-02-30'` 会被转为默认零值 `'0000-00-00'`。
    - 对于 `DATETIME` 类型，非法值可能被转为 `'0000-00-00 00:00:00'`。
    - 对于数值类型如 `INT`，非法输入可能会被转为 `0`。
  - 需要注意的是，这种行为通常依赖于当前 MySQL 的 SQL 模式（SQL Mode）设置。如果启用了严格模式（如 `STRICT_ALL_TABLES` 或 `STRICT_TRANS_TABLES`），MySQL 将不会自动转换非法值，而是直接抛出错误，阻止插入非法数据。
- **非法值处理机制**
  - 当插入超出数据类型范围的值时，MySQL 确实会将其转换为该类型的 **"零值"**（zero value）
  - 此行为是 MySQL 的默认处理方式（非严格模式下）
  - 这个机制仅适用于 **标量值插入**。对于如 `ENUM` 类型的非法选项，MySQL 会转换为空字符串（`''`）而非零值。
- **各类型零值示例**

| 数据类型   | 零值示例                |
| ---------- | ----------------------- |
| `DATE`     | `'0000-00-00'`          |
| `DATETIME` | `'0000-00-00 00:00:00'` |
| `INT`      | `0`                     |
| `VARCHAR`  | 空字符串 `''`           |
| `TIME`     | `'00:00:00'`            |
| `YEAR`     | `0000`                  |

- **SQL 模式的影响**
  - **非严格模式（默认）**：执行自动转换（零值替代）
  - **严格模式**（通过 `SET sql_mode = 'STRICT_ALL_TABLES'` 启用）：直接抛出错误，拒绝插入非法值

```sql
-- 启用严格模式示例
SET sql_mode = 'STRICT_TRANS_TABLES';
```

- **日期类型特殊性**
  - `0000-00-00` 在严格模式下会被视为非法日期（需设置 `ALLOW_INVALID_DATES` 规避）
- **实际生产建议**
  - 避免数据静默损坏
  - 强制前端进行有效值校验

```sql
-- 推荐始终启用严格模式
SET GLOBAL sql_mode = 'STRICT_ALL_TABLES,NO_ENGINE_SUBSTITUTION';
```

- **零值陷阱**
  - 零值（如 `0`/`0000-00-00`）可能被业务逻辑误判为有效数据，建议：
  - 使用 `NOT NULL` 约束
  - 添加 `CHECK` 条件验证范围
