---
title: 接口测试工具Insomnia入门使用
date: 2023/09/27 22:22
updated: 2023/09/27 22:22
categories:
- [技术, 示例]
tags:
- 软件测试
- 测试工具
- Insomnia
---

## 安装

打开官网的下载地址：[Download - Insomnia](https://insomnia.rest/download)

下载对应平台的安装包，然后安装完打开界面，新建一个**文档类型**的项目空间。

![新建项目空间](./assets/image-20230927222418466.png)



进入项目空间后，默认是在DESIGN菜单的，先点击DEBUG菜单进入调试工作区。

![空间布局](./assets/image-20230927222704484.png)



## 基本使用

下面以一个接口为例进行演示。下图中，除了标记③是需要借助插件来实现自己校验功能，其余的功能都是通过Insomnia自带实现的。

![image-20230927222942034](./assets/image-20230927222942034.png)

> 标记③借助的插件是：Response Validator Unit Test Plugin



###　安装插件

- Response Validator Unit Test Plugin
  - 根据 Jsonpath 校验响应结果（已二开开源，支持区间和集合校验）

- faker 
  - 测试数据 Mock（底层是 faker.js）
- mockchineseidcard Plugin 
  - 生成符合校验的大陆身份证(可根据是否成年和性别区分（每次请求都会变化）
- response parse json 
  - 将结果响应中 Json 字符串转成 Json 对象
- Runner 
  - 支持批量执行目录下的用例
- SM3 Plugin 
  - 支持SM3散列而开发的插件（已开源）
- Postman Export
  - 导出 Postman 格式




![插件列表](./assets/image-20230927223310137.png)



#### Response Validator Unit Test Plugin使用

在请求的接口 Headers 上，添加一个Header

- key是固定值，表示要开始自动校验。
- value是要校验的JsonPath规则。

示例：Key:INSOMNIA-RESPONSE-VALIDATOR   value:校验规则的JsonPath



![校验规则](./assets/image-20230927223458593.png)



![校验说明](./assets/image-20230927223552194.png)

> JsonPath示例说明：两个标记数字相同则是一处校验点。

下面是JsonPath校验对应的解释：

```json
{
 "$.respBody.flag": "1", #固定值校验
 "$.respBody.psce_fraud_jsrcu_score": [
 "range", #range，范围校验(300到850这种），这里表示校验在[300,850]的区间 "300", "850" ],
 "$.respBody.ppre_fraud_jsrcu_label": [
 "valid", #valid,集合校验(校验非连续1,3,7这种），这里表示校验{0，1}，连续 0, 1 ],
 "$.respHead.resp_cd": "000000", #固定值校验 "$.respHead.resp_msg": [
 "valid", #集合校验，单个值可用固定值校验代替
 "处理成功" ],
 "$.respHead.comprs": "0" #固定值校验
}
```



(本文完)
