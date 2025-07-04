# [0064. MySQL 数据类型介绍](https://github.com/Tdahuyou/TNotes.sql/tree/main/notes/0064.%20MySQL%20%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E4%BB%8B%E7%BB%8D)

<!-- region:toc -->

- [1. 📝 概述](#1--概述)
- [2. 📒 数值类型](#2--数值类型)
  - [2.1. ⚙️ 整数类型 - `TINYINT`、`SMALLINT`、`MEDIUMINT`、`INT`、`BIGINT`](#21-️-整数类型---tinyintsmallintmediumintintbigint)
  - [2.2. ⚙️ 浮点数类型和定点数类型 - `FLOAT`、`DOUBLE`、`DECIMAL`](#22-️-浮点数类型和定点数类型---floatdoubledecimal)
- [3. 📒 日期与时间类型](#3--日期与时间类型)
  - [3.1. ⚙️ `YEAR`](#31-️-year)
  - [3.2. ⚙️ `DATE`](#32-️-date)
- [4. 📒 文本字符串类型](#4--文本字符串类型)
- [5. 📒 二进制字符串类型](#5--二进制字符串类型)

<!-- endregion:toc -->

## 1. 📝 概述

- 数据库表由多列字段构成，每一个字段都有对应的数据类型。
- 指定了字段的数据类型之后，也就决定了向字段插入的数据内容。比如：
  - 订单数量，可以将其设置为整数类型
  - 身份证号码，可以将其设置为字符串类型
- 不同的数据类型也决定了 MySQL 在存储它们的时候使用的方式，以及在使用它们的时候选择什么运算符号进行运算。
- 选择数据类型时应考虑存储效率、精度需求、计算性能等因素，例如金额计算推荐使用 `DECIMAL`，大文本用 `TEXT`，状态标识可用 `ENUM`。
- 每一个类型都有合法的取值范围，当指定确实不合法的值时系统将“零”值插入到数据库中。

## 2. 📒 数值类型

- 数值类型：包括整数类型 TINYINT、SMALLINT、MEDIUMINT、INT、BIGINT、浮点小数数据类型 FLOAT 和 DOUBLE，定点小数类型 DECIMAL。

### 2.1. ⚙️ 整数类型 - `TINYINT`、`SMALLINT`、`MEDIUMINT`、`INT`、`BIGINT`

- 数值型数据类型主要用来存储数字，MySQL 提供了多种数值数据类型，不同的数据类型提供不同的取值范围，可以存储的值范围越大，其所需要的存储空间也会越大。
- 可选 `UNSIGNED` 属性表示无符号数。
- 可选 `ZEROFILL` 属性用零填充显示宽度。
- 整数类型的属性字段可以添加 `AUTO_INCREMENT` 自增约束条件。

| 类型名称        | 说明           | 存储需求 |
| --------------- | -------------- | -------- |
| `TINYINT`       | 很小的整数     | 1 字节   |
| `SMALLINT`      | 小的整数       | 2 字节   |
| `MEDIUMINT`     | 中等大小的整数 | 3 字节   |
| `INT`/`INTEGER` | 普通大小的整数 | 4 字节   |
| `BIGINT`        | 大整数         | 8 字节   |

- 取值范围

| 类型名称 | 有符号范围 | 无符号范围 |
| --- | --- | --- |
| `TINYINT` | -128~127 | 0~255 |
| `SMALLINT` | -32768~32767 | 0~65535 |
| `MEDIUMINT` | -8388608~8388607（-800w~800w） | 0~16777215（0~1600w） |
| `INT`/`INTEGER` | -2147483648~2147483647（-21e~21e - 最常用） | 0~4294967295（0~42e - 最常用） |
| `BIGINT` | -9223372036854775808~9223372036854775807（-2^63 ~ 2^63，9wee~9wee） | 0~18446744073709551615（0 ~ 2^64，18wee） |

### 2.2. ⚙️ 浮点数类型和定点数类型 - `FLOAT`、`DOUBLE`、`DECIMAL`

- MySQL 中使用浮点数和定点数来表示小数。
  - 浮点数类型有两种：单精度浮点类型（FLOAT）和双精度浮点类型（DOUBLE）​。
  - 定点数类型只有一种：DECIMAL。
- 浮点数类型和定点数类型都可以用（M，N）来表示。
  - M 称为精度，表示总共的位数。
  - N 称为标度，表示小数的位数。
- `DECIMAL` 类型不同于 `FLOAT` 和 `DOUBLE`，`DECIMAL` 实际是以串存放的，可能的最大取值范围与 `DOUBLE` 一样，但是其有效的取值范围由 M 和 D 的值决定。
  - 如果改变 M 而固定 D，则其取值范围将随 M 的变大而变大。
  - 如 `DECIMAL(5,2)` 表示共 5 位，2 位小数
- 四舍五入
  - 不论是定点数还是浮点数类型，如果用户指定的精度超出精度范围，则会四舍五入。
- 精度
  - FLOAT 和 DOUBLE 在不指定精度时，默认会按照实际的精度（由计算机硬件和操作系统决定）​。
  - DECIMAL 若不指定精度则默认为(10,0)。
  - 浮点数相对于定点数的优点是在长度一定的情况下，浮点数能够表示更大的数据范围；它的缺点是会引起精度问题。
- 应用场景
  - **浮点类型适合科学计算，定点类型适合财务等精确计算。**
  - 在 MySQL 中，定点数以字符串形式存储，在对精度要求比较高的时候（如货币、科学数据等）使用 DECIMAL 的类型比较好，另外两个浮点数进行减法和比较运算时容易出问题，所以在使用浮点数时需要注意，并尽量避免做浮点数比较。

| 类型名称        | 说明               | 存储需求 |
| --------------- | ------------------ | -------- |
| `FLOAT`         | 单精度浮点数       | 4 字节   |
| `DOUBLE`        | 双精度浮点数       | 8 字节   |
| `DECIMAL(M, D)` | 压缩的“严格”定点数 | M+2 字节 |

- 取值范围

| 类型名称 | 有符号范围 | 无符号范围 |
| --- | --- | --- |
| `FLOAT` | -3.402823466E+38 ~ -1.175494351E-38 | 0 和 1.175494351E-38 ~ 3.402823466E+38 |
| `DOUBLE` | -1.7976931348623157E+308 ~ -2.2250738585072014E-308 | 0 和 2.2250738585072014E-308 ~ 1.7976931348623157E+308 |

```sql
CREATE TABLE test1 (x FLOAT(5, 1), y DOUBLE(5, 1), z DECIMAL(5, 1));

INSERT INTO test1 VALUES(5.12, 5.15, 5.123);
SELECT * FROM test1;
-- +------+------+------+
-- | x    | y    | z    |
-- +------+------+------+
-- |  5.1 |  5.2 |  5.1 |
-- +------+------+------+

INSERT INTO test1 VALUES(5.15, 5.15, 5.15);
SELECT * FROM test1;
-- +------+------+------+
-- | x    | y    | z    |
-- +------+------+------+
-- |  5.1 |  5.2 |  5.1 |
-- |  5.2 |  5.2 |  5.2 |
-- +------+------+------+
```

## 3. 📒 日期与时间类型

- MySQL 中有多种表示日期的数据类型，主要有 DATETIME、DATE、TIMESTAMP、TIME 和 YEAR。

### 3.1. ⚙️ `YEAR`

- `YEAR`：年份值（1 字节）
  - 当只记录年信息的时候，可以只使用 YEAR 类型，而没有必要使用 DATE。
  - YEAR 类型是一个单字节类型，用于表示年，在存储时只需要 1 字节。
- 可以使用各种格式指定 YEAR 值
  - 以 **4 位字符串或者 4 位数字** 格式表示的 YEAR，范围为 `'1901'～'2155'`。
    - 输入格式为 `'YYYY'` 或者 `YYYY`。
    - 例如，输入 `'2010'` 或 `2010`，插入到数据库的值均为 `2010`。
  - 以 **2 位字符串** 格式表示的 YEAR，范围为 `'00'~'99'`。
    - `'00'～'69'` 和 `'70'～'99'` 范围的值分别被转换为 `2000 ～ 2069` 和 `1970 ～ 1999` 范围的 YEAR 值。
    - `'0'` 与 `'00'` 的作用相同。
    - 插入超过取值范围的值将被转换为 2000。
  - 以 **2 位数字** 表示的 YEAR，范围为 `1 ～ 99`。
    - `1 ～ 69` 和 `70 ～ 99` 范围的值分别被转换为 `2001 ～ 2069` 和 `1970 ～ 1999` 范围的 YEAR 值。
    - 注意：在这里 0 值将被转换为 0000，而不是 2000。

### 3.2. ⚙️ `DATE`

- `DATE`：存储日期值（'YYYY-MM-DD'）
- `TIME`：存储时间值（'HH:MM:SS'）
- `DATETIME`：日期+时间（'YYYY-MM-DD HH:MM:SS'）
- `TIMESTAMP`：时间戳（自动记录数据修改时间）

## 4. 📒 文本字符串类型

- `CHAR`：定长字符串（0~255 字符），适合固定长度数据
- `VARCHAR`：可变长字符串（0~65535 字符），节省存储空间
- `TEXT`：长文本数据（支持 `TINYTEXT`, `TEXT`, `MEDIUMTEXT`, `LONGTEXT`）
- `ENUM`：枚举类型（从预定义列表选择单个值）
- `SET`：集合类型（从预定义列表选择多个值）
- 字符串类型又分为文本字符串和二进制字符串。

## 5. 📒 二进制字符串类型

- `BINARY`：定长二进制数据（类似 `CHAR`）
- `VARBINARY`：可变长二进制数据（类似 `VARCHAR`）
- `BLOB`：二进制大对象（支持 `TINYBLOB`, `BLOB`, `MEDIUMBLOB`, `LONGBLOB`）
- 用于存储图片、音频、加密数据等二进制内容
