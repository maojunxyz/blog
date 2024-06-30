---
title: GitHub搜索技巧
date: 2024/03/27 10:57:45
updated: 024/03/27 10:57:45
categories:
- [技术, 网络]
tags:
- GitHub
---

## GitHub 搜索技巧

这里可以了解一些搜索语法，会对你的精准查询有所帮助。

### 查询大于或小于另一个值的值

您可以使用 `>`，`>=`，`<`，和 `<=` 搜索是大于，大于或等于，小于和小于或等于另一个值的值。

| 匹配条件 | 举例                                                         |
| -------- | ------------------------------------------------------------ |
| `>` *n*  | cats[ **stars:> 1000**](https://github.com/search?utf8=%E2%9C%93&q=cats+stars%3A%3E1000&type=Repositories) 匹配超过 1000 star 的包含 “cats” 关键字的仓库。 |
| `>=` *n* | [**cats topics:> = 5**](https://github.com/search?utf8=%E2%9C%93&q=cats+topics%3A%3E%3D5&type=Repositories) 匹配具有 5 个或更多主题的单词 “cats” 的存储库。 |
| `<` *n*  | ****[**cats size:<10000**](https://github.com/search?utf8=%E2%9C%93&q=cats+size%3A%3C10000&type=Code) 匹配小于 10 KB 的文件中带有 “cats” 字样的代码。 |
| `<=` *n* | [**cats stars:<= 50**](https://github.com/search?utf8=%E2%9C%93&q=cats+stars%3A%3C%3D50&type=Repositories) 匹配具有 50 个或更少 star 的包含 “cats” 关键字的仓库。 |

您还可以使用[范围查询](https://help.github.com/en/articles/understanding-the-search-syntax#query-for-values-between-a-range)来搜索大于或等于，或小于或等于另一个值的值。

| 搜索语法  | 举例                                                         |
| --------- | ------------------------------------------------------------ |
| *n* `..*` | ****[**cats stars:10 .. \***](https://github.com/search?utf8=%E2%9C%93&q=cats+stars%3A10..*&type=Repositories) 相当于 `stars:>=10` 并匹配具有 10 个或更多星的 “猫” 一词的存储库。 |
| `*..` *n* | [cats **stars:\* .. 10**](https://github.com/search?utf8=%E2%9C%93&q=cats+stars%3A%22*..10%22&type=Repositories) 相当于 `stars:<=10` 和匹配具有 10 个或更少恒星的单词 “cats” 的存储库。 |

### [查询范围之间的值](https://help.github.com/en/articles/understanding-the-search-syntax#query-for-values-between-a-range)

您可以使用范围语法搜索范围内的值，其中第一个数字 *n* 是最低值，第二个数字是最高值。*n*`..`*n*

| 询问         | 举例                                                         |
| ------------ | ------------------------------------------------------------ |
| *n* `..` *n* | ****[**cats stars:10..50**](https://github.com/search?utf8=%E2%9C%93&q=cats+stars%3A10..50&type=Repositories) 匹配存储库，单词 “cats”，有 10 到 50 颗星。 |

### [查询日期](https://help.github.com/en/articles/understanding-the-search-syntax#query-for-dates)

您可以搜索日期，除另一个日期，或者那年秋天日期范围内较早或较晚的是，通过使用 `>`，`>=`，`<`，`<=`，和[范围查询](https://help.github.com/en/articles/understanding-the-search-syntax#query-for-values-between-a-range)。日期格式必须遵循 ISO8601 标准，即 `YYYY-MM-DD`（年 - 月 - 日）。

| 询问                       | 举例                                                         |
| -------------------------- | ------------------------------------------------------------ |
| `>` *YYYY-MM-DD*           | [cats **created:> 2016-04-29**](https://github.com/search?utf8=%E2%9C%93&q=cats+created%3A%3E2016-04-29&type=Issues) 匹配 2016 年 4 月 29 日之后创建的 “cats” 一词的问题。 |
| `>=` *YYYY-MM-DD*          | ****[**cats created:> = 2017-04-01**](https://github.com/search?utf8=%E2%9C%93&q=cats+created%3A%3E%3D2017-04-01&type=Issues) 匹配 2017 年 4 月 1 日或之后创建的 “cats” 一词的问题。 |
| `<` *YYYY-MM-DD*           | [**cats pushed:<2012-07-05**](https://github.com/search?q=cats+pushed%3A%3C2012-07-05&type=Code&utf8=%E2%9C%93) 匹配代码与 2012 年 7 月 5 日之前推送到的存储库中的 “cats” 一词。 |
| `<=` *YYYY-MM-DD*          | [cats **created**](https://github.com/search?utf8=%E2%9C%93&q=cats+created%3A%3E%3D2017-04-01&type=Issues)[:**<= 2012-07-04**](https://github.com/search?utf8=%E2%9C%93&q=cats+created%3A%3C%3D2012-07-04&type=Issues) 与 2012 年 7 月 4 日或之前创建的 “cats” 一词相匹配。 |
| *YYYY-MM-DD .. YYYY-MM-DD* | [**cats pushed**](https://github.com/search?q=cats+pushed%3A%3C2012-07-05&type=Code&utf8=%E2%9C%93)[:**2016-04-30..2016-07-04**](https://github.com/search?utf8=%E2%9C%93&q=cats+pushed%3A2016-04-30..2016-07-04&type=Repositories) 匹配 2016 年 4 月底到 7 月期间被推到 “cats” 字样的知识库。 |
| *YYYYYYYY-MM-DD*`..*`      | [**cats created**](https://github.com/search?utf8=%E2%9C%93&q=cats+created%3A2012-04-30..*&type=Issues)[:**2012-04-30 .. \***](https://github.com/search?utf8=%E2%9C%93&q=cats+created%3A2012-04-30..*&type=Issues) 匹配 2012 年 4 月 30 日之后创建的包含 “cats” 字样的问题。 |
| `*..`*YYYYYYYY-MM-DD*      | [**cats created**](https://github.com/search?utf8=%E2%9C%93&q=cats+created%3A2012-04-30..*&type=Issues)[:*** .. 2012-04-30**](https://github.com/search?utf8=%E2%9C%93&q=cats+created%3A*..2012-07-04&type=Issues) 匹配 2012 年 7 月 4 日之前创建的包含 “cats” 字样的问题。 |

您还可以 `THH:MM:SS+00:00` 在日期之后添加可选时间信息，以按小时，分钟和秒搜索。那是 `T`，然后是 `HH:MM:SS`（小时 - 分 - 秒）和 UTC 偏移（`+00:00`）。

| 询问                                      | 例                                                           |
| ----------------------------------------- | ------------------------------------------------------------ |
| `YYYY - MM - DD T HH : MM : SS + 00 : 00` | [cats **created**](https://github.com/search?utf8=%E2%9C%93&q=cats+created%3A%3E%3D2017-04-01&type=Issues)[:**2017-01-01T01：00：00 + 07：00..2017-03-01T15：30：15 + 07:00**](https://github.com/search?utf8=%E2%9C%93&q=cats+created%3A2017-01-01T01%3A00%3A00%2B07%3A00..2017-03-01T15%3A30%3A15%2B07%3A00&type=Issues) **** 匹配 2017 年 1 月 1 日凌晨 1 点之间创建的问题，UTC 偏移量为 `07:00`3 月 1 日，2017 年下午 3 点，UTC 偏移量为 `07:00`。 |
| `YYYY - MM - DD T HH : MM : SS Z`         | [cats **created**](https://github.com/search?utf8=%E2%9C%93&q=cats+created%3A%3E%3D2017-04-01&type=Issues)[:**2016-03-21T14：11：00Z..2016-04-07T20：45：00Z**](https://github.com/search?utf8=%E2%9C%93&q=cats+created%3A2016-03-21T14%3A11%3A00Z..2016-04-07T20%3A45%3A00Z&type=Issues) 匹配 2016 年 3 月 21 日下午 2:11 和 2106 年 4 月 7 日晚上 8:45 之间创建的问题。 |

### [排除某些结果](https://help.github.com/en/articles/understanding-the-search-syntax#exclude-certain-results)

您可以使用 `NOT` 语法排除包含特定单词的结果。该 `NOT` 操作只能用于字符串的关键词。它不适用于数字或日期。

| 询问  | 例子                                                         |
| ----- | ------------------------------------------------------------ |
| `NOT` | [hello NOT world](https://github.com/search?q=hello+NOT+world&type=Repositories) 匹配有 “hello” 这个词而没有 “world” 这个词的仓库。 |

### 对带有空格的查询使用引号

如果您的搜索查询包含空格，则需要用引号括起来。例如：

- [cats NOT "hello world"](https://github.com/search?utf8=%E2%9C%93&q=cats+NOT+%22hello+world%22&type=Repositories) 匹配存储库的单词 “cats” 而不是 “hello world”。
- [构建标签：“bug fix”](https://github.com/search?utf8=%E2%9C%93&q=build+label%3A%22bug+fix%22&type=Issues) 与 “build” 一词的问题相匹配，其标签为 “bug fix”。





（完）

