---
title: Gatling和Jmeter的区别
date: 2022/03/30 11:23:38
updated: 2022/03/30 11:23:38
categories:
- [技术, Gatling]
tags:
- Gatling
- Jmeter
- 性能测试
---



## 对比

 JMeter 和 Gatling 的比较：

| 比较项                  | Jmeter                                                       | Gatling（开源版）                                            |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| UI界面                  | 支持                                                         | 不支持                                                       |
| 无代码测试              | 执行jmx                                                      | 需要具备 Scala语言的技能                                     |
| 脚本更改                | 通过 JMeter UI更改                                           | 通过编辑器更改gatling脚本                                    |
| 分布式测试              | 支持                                                         | 支持                                                         |
| 应用程序类型            | 支持多类型程序，可插件扩展                                   | 支持少类型程序，可代码扩展                                   |
| CI/CD 支持              | 支持                                                         | 支持                                                         |
| 定制报告                | 可以自定义报告。                                             | 无法自定义报告                                               |
| 版本控制                | 不适合Git/SVN源代码管理                                      | 适合Git/SVN源代码管理                                        |
| 实时测试运行监控        | 可以进行实时测试运行监控                                     | 无法进行实时测试运行监视                                     |
| 插件                    | 有大量的[JMeter插件](https://www.blazemeter.com/blog/top-ten-jmeter-plugins)可用。 | 数量较少[gatling插件 ](https://gatling.io/docs/gatling/reference/current/extensions/) |
| 从 HAR 文件生成测试计划 | JMeter 无法从本机（[ 第三方支持](https://converter.blazemeter.com/)）的 HAR 文件生成测试计划。 | gatling 录制 （[链接](https://gatling.io/docs/gatling/reference/current/http/recorder/)） 可以从 HAR 文件生成负载测试脚本。 |
| 脚本组件化              | 不适合                                                       | scala语言支持                                                |
| 第三方脚本录制          | 支持第三方插件录制 [浏览器插件](https://chrome.google.com/webstore/detail/blazemeter-the-continuous/mbopgmdnpcbohhpnfglgohlbhfongabi). | 自身支持录制                                                 |



## 总结

- Jmeter的脚本要依赖与Jmerter Ui去编辑和修改。
- Gatling可以通过编辑器直接修改源码。
- 同样都依赖与Java环境。
- Gatling压测代码可以使用GIT/SVN进行版本管理。



（完）
