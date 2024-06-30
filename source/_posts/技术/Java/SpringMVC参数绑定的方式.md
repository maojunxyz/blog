---
title: SpringMVC参数绑定的方式
date: 2019/11/04 17:20:07
updated: 2019/11/04 17:20:07
categories:
- [技术, Java]
tags:
- SpringMVC
- Java
---



## 名称匹配接收参数

入参和接收参数名称一致。

```java
@Controller
@RequestMapping("/param")
public class TestParamController {
    private static final Logger logger = LoggerFactory.getLogger(TestParamController.class);
    /**
     * 请求参数名和Controller方法的参数一致
     * produces 设置返回参数的编码格式可以设置返回数据的类型以及编码，可以是json或者xml
     * {
     *     @RequestMapping(value="/xxx",produces = {"application/json;charset=UTF-8"})
     *      或
     *     @RequestMapping(value="/xxx",produces = {"application/xml;charset=UTF-8"})
     *      或
     *     @RequestMapping(value="/xxx",produces = "{text/html;charset=utf-8}")
     * }
     * @param name 用户名
     * @param pwd 密码
     * @return
     *
     */
    @RequestMapping(value = "/add", method = RequestMethod.GET, produces = {"application/json;charset=UTF-8"})
    @ResponseBody
    public String addUser(String name, String pwd){
        logger.debug("name:" + name + ",pwd:" + pwd);
        return "name:" + name + ",pwd:" + pwd;
    }

}
```

请求方式：

```curl
curl http://localhost:8080/sty/param/add.action?name=张三&pwd=123456
```



## 对象属性名称匹配接收参数

Java实体类定义：

```java
@Data
public class User {
    private String name;
    private String pwd;
}
```



入参和接收对象的参数一致。

```java
@Controller
@RequestMapping("/param")
public class TestParamController {
    private static final Logger logger = LoggerFactory.getLogger(TestParamController.class);
    /**
     * 请求参数名和Controller方法的参数一致
     * produces 设置返回参数的编码格式可以设置返回数据的类型以及编码，可以是json或者xml
     * }
     * @param user 用户信息
     * @return
     *
     */
    @RequestMapping(value = "/addByObject", method = RequestMethod.GET, produces = {"application/json;charset=UTF-8"})
    @ResponseBody
    public String addUserByObject(User user){
        logger.debug("name:" + user.getName() + ",pwd:" + user.getPwd());
        return "name:" + user.getName() + ",pwd:" + user.getPwd();
    }
}
```

请求方式：

```curl
curl http://localhost:8080/sty/param/addByObject.action?name=张三&pwd=123456
```



## 重命名接收参数

当请求参数名与方法参数名不一致时。

```java
/**
 * 自定义方法参数名-当请求参数名与方法参数名不一致时
 * @param u_name 用户名
 * @param u_pwd 密码
 * @return
 */
@RequestMapping(value = "/addByDifName", method = RequestMethod.GET, produces = {"application/json;charset=UTF-8"})
@ResponseBody
public String addUserByDifName(@RequestParam("name") String u_name, @RequestParam("pwd")String u_pwd){
    logger.debug("name:" + u_name + ",pwd:" + u_pwd);
    return "name:" + u_name + ",pwd:" + u_pwd;
}
```

请求方式：

```curl
curl http://localhost:8080/sty/param/addUserByDifName.action?name=张三&pwd=123456
```



## HttpServletRequest接收参数

```java
/**

 * 通过HttpServletRequest接收

 * @param request

 * @return

 */

@RequestMapping(value = "/addByHttpServletRequest", method = RequestMethod.GET, produces = {"application/json;charset=UTF-8"})

@ResponseBody

public String addUserByHttpServletRequest(HttpServletRequest request){

    String name = request.getParameter("name");

    String pwd = request.getParameter("pwd");

    logger.debug("name:" + name + ",pwd:" + pwd);

    return "name:" + name + ",pwd:" + pwd;

}
```

请求方式：

```curl
curl http://localhost:8080/sty/param/addByHttpServletRequest.action?name=张三&pwd=123456
```



## @PathVariable接收路径中的参数

```java
/**

 * 通过@PathVariable获取路径中的参数 

 * @param name 用户名

 * @param pwd 密码

 * @return

 */

@RequestMapping(value = "/add/{name}/{pwd}", method = RequestMethod.GET, produces = {"application/json;charset=UTF-8"})

@ResponseBody

public String addUserByPathVariable(@PathVariable String name, @PathVariable String pwd){

    logger.debug("name:" + name + ",pwd:" + pwd);

    return "name:" + name + ",pwd:" + pwd;

}
```

请求方式：

```curl
curl http://localhost:8080/sty/param/add/zhangsan/123456.action
```



## @RequestBody接收对象

### 接收JSON对象

需要依赖jackson-databind.jar，并设置json格式的支持。

```java
/**

 * RequestBody-JSON 对象方式

 * @param user

 * @return

 */

@RequestMapping(value = "/addByObjectJSON", produces = {"application/json;charset=UTF-8"})

@ResponseBody

public String addUserByObjectJSON(@RequestBody User user){

    logger.debug("name:" + user.getName() + ",pwd:" + user.getPwd());

    return "name:" + user.getName() + ",pwd:" + user.getPwd();

}
```

请求方式：

```curl
curl --request POST \
  --url http://localhost:8080/sty/param/addByObjectJSON.action \
  --header 'content-type: application/json' \
  --data '{
  "name": "zhangsan",
  "pwd": "123456"
}'
```

### 接收JSON数组

```java
/**
 * RequestBody-JSON List对象方式
 * @param users
 * @return
 */
@RequestMapping(value = "/addByListJSON", produces = {"application/json;charset=UTF-8"})
@ResponseBody
public String addUsersByListJSON(@RequestBody List<User> users){
    StringBuilder sb = new StringBuilder("{");
    if(null != users){
        for(User user : users){
            sb.append("{" + "name:" + user.getName() + ",pwd:" + user.getPwd() + "}");
        }
    }
    sb.append("}");
    logger.debug(sb.toString());
    return sb.toString();
}
```

请求方式：

```curl
curl --request POST \
  --url http://localhost:8080/sty/param/addByObjectJSON.action \
  --header 'content-type: application/json' \
  --data '[
  {
    "name": "zhangsan",
    "pwd": "123456"
  },
  {
    "name": "lisi",
    "pwd": "654321"
  }
]'
```

### 接收Map对象

```java
/**

 * RequestBody-JSON Map对象方式

 * @param users

 * @return

 */

@RequestMapping(value = "/addByMapJSON", produces = {"application/json;charset=UTF-8"})

@ResponseBody

public String addUsersByMapJSON(@RequestBody Map<String, User> users){

    StringBuilder sb = new StringBuilder("{");

    if(null != users){

        Iterator it = users.keySet().iterator();

        while(it.hasNext()){

            User user = users.get(it.next());

            sb.append("{" + "name:" + user.getName() + ",pwd:" + user.getPwd() + "}");

        }

    }

    sb.append("}");

    logger.debug(sb.toString());

    return sb.toString();

}
```

请求方式：

```curl
curl --request POST \
  --url http://localhost:8080/sty/param/addByObjectJSON.action \
  --header 'content-type: application/json' \
  --data '[
  {
    "name": "zhangsan",
    "pwd": "123456"
  },
  {
    "name": "lisi",
    "pwd": "654321"
  }
]'
```



（完）
