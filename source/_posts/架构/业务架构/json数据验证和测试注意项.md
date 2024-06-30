---
title: JSON 数据验证和测试注意项
date: 2023/08/03 16:02
updated: 2023/08/03 16:02
categories:
- [架构, 业务架构]
tags:
- JSON
- 软件测试
---
##  介绍

在接口调用中，JSON 是主流的数据传输交换格式。 JSON 官方对其的介绍内容是：

> JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。 易于人阅读和编写。同时也易于机器解析和生成。 它基于JavaScript Programming Language, Standard ECMA-262 3rd Edition - December 1999的一个子集。 JSON采用完全独立于语言的文本格式，但是也使用了类似于C语言家族的习惯（包括C, C++, C#, Java, JavaScript, Perl, Python等）。 这些特性使JSON成为理想的数据交换语言。


## JSON数据测试

JSON 的数据类型包括以下6类：

1. 对象（Object）：用花括号 `{}` 表示，表示一组键值对的集合。
2. 数组（Array）：用方括号 `[]` 表示，表示一组值的有序集合。
3. 字符串（String）：用双引号 `""` 或单引号 `''` 包裹的字符序列。
4. 数值（Number）：表示数字，可以是整数或浮点数。
5. 布尔值（Boolean）：表示真或假，只有两个可能的取值：`true` 或 `false`。
6. 空值（null）：表示空值，只有一个取值：`null`。

所以，在对 JSON 数据测试时，可以依据 **等价类分析** 和 **边界值分析** 设计测试数据和用例。

例如,等价类测试数据：
```json
{
  "string_example": "Hello, World!",
  "number_example": 42,
  "boolean_example": true,
  "null_example": null,
  "array_example": [1, 2, 3],
  "nested_object_example": {
    "key1": "value1",
    "key2": 123,
    "key3": true,
    "key4": null
  }
}
```

边界值，以 Java 的数据边界值为例：

```json
{
  "empty_string_example": "",
  "large_string_example":"abcdefghijklmnopqretuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
  "max_long_example": 9223372036854775807, 
  "min_long_example": -9223372036854775808 
  "max_double_example": 1.7976931348623157e+308, 
  "min_double_example": 4.9e-324, 
  "large_array_example": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15]
}
```

> 注意：Java 基本数据类型不存在越界，只会溢出。它们取值范围的最大值+1的结果都是它们取值范围的最小值，反之相同。

## 总结

在对 JSON 数据类型进行分析设计后，还可以针对业务场景进行测试数据设计。比如需求和技术文档中明确了 **foo** 字段是 **Object** 类型，但如果传了 **String** 类型，会有什么异常或兜底逻辑，反之相同。在测试过程中需不断分析使用场景，以便提高系统的 **鲁棒性** 和用户的 **体验性**。

> 鲁棒是Robust的音译，也就是健壮和强壮的意思。 它也是在异常和危险情况下系统生存的能力。

（本文完）
