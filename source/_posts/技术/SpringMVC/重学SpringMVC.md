---
title: 重学SpringMVC
date: 2016/03/04 14:32:37
updated: 2016/03/04 14:32:37
categories:
- [技术, SpringMVC]
tags:
- SpringMVC
---



## SpringMVC 工作流程

1. 用户发送请求至前端控制器 DispatcherServlet。
2. DispatcherServlet 收到请求调用 HandlerMapping 处理器映射器。
3. 处理器映射器找到具体的处理器(可以根据 xml 配置、注解进行查找)，生成处理器及处理器拦截器(如果有则生成)一并返回给 DispatcherServlet。
4. DispatcherServlet 调用 HandlerAdapter 处理器适配器。
5. HandlerAdapter 经过适配调用具体的处理器(Controller，也叫后端控制器)
6. Controller 执行完成返回 ModelAndView。
7. HandlerAdapter 将 controller 执行结果 ModelAndView 返回给 DispatcherServlet。
8. DispatcherServlet 将 ModelAndView 传给 ViewReslover 视图解析器。
9. ViewReslover 解析后返回具体 View。
10. DispatcherServlet 根据 View 进行渲染视图（即将模型数据填充至视图中）。
11. DispatcherServlet 响应用户。



![SpringMVC 工作流程](./assets/image-20240419063724062.png)



## Spring MVC的主要组件



Handler：也就是处理器。它直接应对着MVC中的C也就是Controller层，它的具体表现形式有很多，可
以是类，也可以是方法。在Controller层中@RequestMapping标注的所有方法都可以看成是一个
Handler，只要可以实际处理请求就可以是Handler

- HandlerMapping

initHandlerMappings(context)，处理器映射器，根据用户请求的资源uri来查找Handler的。在
SpringMVC中会有很多请求，每个请求都需要一个Handler处理，具体接收到一个请求之后使用哪个
Handler进行，这就是HandlerMapping需要做的事。

- HandlerAdapter

initHandlerAdapters(context)，适配器。因为SpringMVC中的Handler可以是任意的形式，只要能处
理请求就ok，但是Servlet需要的处理方法的结构却是固定的，都是以request和response为参数的方
法。如何让固定的Servlet处理方法调用灵活的Handler来进行处理呢？这就是HandlerAdapter要做的
事情。
Handler是用来干活的工具；HandlerMapping用于根据需要干的活找到相应的工具；HandlerAdapter
是使用工具干活的人。

- HandlerExceptionResolver

initHandlerExceptionResolvers(context)， 其它组件都是用来干活的。在干活的过程中难免会出现问
题，出问题后怎么办呢？这就需要有一个专门的角色对异常情况进行处理，在SpringMVC中就是
HandlerExceptionResolver。具体来说，此组件的作用是根据异常设置ModelAndView，之后再交给
render方法进行渲染。

- ViewResolver

initViewResolvers(context)，ViewResolver用来将String类型的视图名和Locale解析为View类型的视
图。View是用来渲染页面的，也就是将程序返回的参数填入模板里，生成html（也可能是其它类型）
文件。这里就有两个关键问题：使用哪个模板？用什么技术（规则）填入参数？这其实是ViewResolver
主要要做的工作，ViewResolver需要找到渲染所用的模板和所用的技术（也就是视图的类型）进行渲
染，具体的渲染过程则交由不同的视图自己完成。

- RequestToViewNameTranslator

initRequestToViewNameTranslator(context)，ViewResolver是根据ViewName查找View，但有的
Handler处理完后并没有设置View也没有设置ViewName，这时就需要从request获取ViewName了，
如何从request中获取ViewName就是RequestToViewNameTranslator要做的事情了。
RequestToViewNameTranslator在Spring MVC容器里只可以配置一个，所以所有request到
ViewName的转换规则都要在一个Translator里面全部实现。

- LocaleResolver

initLocaleResolver(context)， 解析视图需要两个参数：一是视图名，另一个是Locale。视图名是处理
器返回的，Locale是从哪里来的？这就是LocaleResolver要做的事情。LocaleResolver用于从request
解析出Locale，Locale就是zh-cn之类，表示一个区域，有了这个就可以对不同区域的用户显示不同的
结果。SpringMVC主要有两个地方用到了Locale：一是ViewResolver视图解析的时候；二是用到国际化
资源或者主题的时候。

- ThemeResolver

initThemeResolver(context)，用于解析主题。SpringMVC中一个主题对应一个properties文件，里面
存放着跟当前主题相关的所有资源、如图片、css样式等。SpringMVC的主题也支持国际化，同一个主
题不同区域也可以显示不同的风格。SpringMVC中跟主题相关的类有 ThemeResolver、ThemeSource
和Theme。主题是通过一系列资源来具体体现的，要得到一个主题的资源，首先要得到资源的名称，这
是ThemeResolver的工作。然后通过主题名称找到对应的主题（可以理解为一个配置）文件，这是
ThemeSource的工作。最后从主题中获取资源就可以了。

- MultipartResolver

initMultipartResolver(context)，用于处理上传请求。处理方法是将普通的request包装成
MultipartHttpServletRequest，后者可以直接调用getFile方法获取File，如果上传多个文件，还可以调
用getFileMap得到FileName->File结构的Map。此组件中一共有三个方法，作用分别是判断是不是上传
请求，将request包装成MultipartHttpServletRequest、处理完后清理上传过程中产生的临时资源。

- FlashMapManager

initFlashMapManager(context)，用来管理FlashMap的，FlashMap主要用在redirect中传递参数。
