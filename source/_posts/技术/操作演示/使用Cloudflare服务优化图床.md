---
title: 使用 Cloudflare 服务优化图床
date: 2020/02/22 00:00
updated: 2023/08/26 13:46
categories:
- [技术, 示例]
tags:
- 对象存储
- Cloudflare
---


## 图床优化

使用backblaze-B2搭建自己的图床[^1]获取图片地址后，发现图片的链接难以记忆，并且不是自己的域名。

虽然可以通过域名的`DNS`服务并设置`CNAME`绑定二级域名到`f000.backblazeb2.com`,这样就可以通过自定义域名访问图片地址。比如`img.maojun.xyz/file/test/bar.png`。但是又有一个问题，`/file/test`这个也是固定的，如果直接显示为`img.maojun.xyz/bar.png`，这样就显得清爽多了。所以首先对图片的返回地址做下优化，可以将图床地址绑定成自己的域名。比如图片地址器地址是**img.maojunxyz**。 同时将默认图片地址后的路径`/file/bucketName/foo.png`优化缩短为`foo.png`，这样和图片前缀地址拼接上就是: **img.maojun.xyz/foo.png**。



## 自定义图片地址

这里将使用`Cloudflare`作为`DNS`的服务商。因为`Cloudflare`提供了一系列对站长友好的工具，并且可以免费使用。比如`CDN`,`Worker`,网站信息统计等功能。当然这一步不是必须的,你可以维持你的`DNS`服务提供商,这不会对后续的操作有影响。只需根据下面的步骤作为参考并在`DNS`服务商处做相应的修改即可。

下面以`Cloudflare`做为演示: 如果没有`Cloudflare`账号先注册并登入，`Cloudflare`的官网地址是Cloudflare[^2]。

使用`Cloudflare`的`DNS`解析服务，首先添加域名，点击按钮**Add a Site**,然后根据提示。将`DNS`地址修改成`Cloudflare`提供的地址即可完成绑定解析。

绑定后然后点击自己的域名进入到控制台,点击菜单图标**DNS**，进入`DNS`控制面板。

点击**record**按钮，在输入框中输入并绑定自己的图片域名到`b2`服务地址。 填写完成后，点击**SAVE**按钮保存设置。

等待片刻就可以通过自定义图片域名+后缀访问图片地址了。 前后对比：

```
https://f000.backblazeb2.com/file/bucketName/foo.png # 老的图片链接
https://img.maojun.xyz/file/bucketName/foo.png # 新的图片键接
```



## 配置页面规则

在上面的链接中，访问`https://img.maojun.xyz/file/bucketName/foo.png`，当`bucketName`正确匹配我们自己创建的`buckets`时，可以正常访问到图片链接。 当`bucketName`是其他名称或者不是我们创建的`buckets`名称时，我们让其跳转到错误页面。

```
https://img.maojun.xyz/file/bucketName/foo.png # 正确的，提供服务
https://img.maojun.xyz/file/aaaaaaaa/foo.png  # 不正确的，跳转到404页面
```

可以通过`Cloudflare`的**Page ruler**进行配置。点击域名下的控制面板，点击菜单**Page ruler**进入服务。 点击**Create Page Ruler**按钮，配置如下信息：

| URL                                                          | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [https://img.maojun.xyz/file/imgxyz/](https://web.archive.org/web/20201019223844/https://img.maojun.xyz/file/imgxyz/)* | Cache Level: Standard                                        |
| [https://img.maojun.xyz/file/*/](https://web.archive.org/web/20201019223844/https://img.maojun.xyz/file/*/)* | Forwarding URL (Status Code: 302 - Temporary Redirect, Url: [https://maojun.xyz/404notfound](https://web.archive.org/web/20201019223844/https://maojun.xyz/404notfound)) |

上面的配置信息规则为：

- 当访问`https://img.maojun.xyz/file/imgxyz/*`时，使用标准缓存。
- 当访问`https://img.maojun.xyz/file/*/*`(即除了上面的规则外的地址如：https://img.maojun.xyz/file/abc/*) 就302掉转到指定的404页面。)

> 注意上面的规则顺序不能错，如果第二条规则在上面则会拦截所有地址包括`https://img.maojun.xyz/file/imgxyz/*`。

这样当访问的请求不是来自配置的页面放行规则时就会被`302`跳转到指定的页面。 



## 简化图片链接

通过自定义域名绑定后，链接地址比之前要更加直观。但是后面的长路径依然存在，我们可以想办法把它去除，让最终的链接格式如下：

```
https://img.maojun.xyz/foo.png
```

我们可以使用`Cloudflare`提供的`Workers`服务来重写图片的`URL`地址，让链接地址更加友好和简洁。`Workers`是一个边缘计算服务，允许你在`Cloudflare`全球网络中的边界服务器运行`JavaScript`代码。开发者可以部署`serverless`(轻服务) 的应用程序。

免费版：

- 每天**10万**次请求
- **30**个`Workers`脚本
- 运行在**200**个数据中心
- 免费的**workers.dev**二级域名
- 每次请求最大占用`CPU`运行时间为**10ms**
- 首次请求后最低延迟

在`Cloudflare`的主界面，在右侧位置点击进入**Workers**服务。 点击**Craate a Workers**按钮创建一个新的`Workers`脚本。

在**Scripe**代码框中清除原先代码并粘贴以下内容：

```js
'use strict';
const b2Domain = 'img.domain.com'; // configure this as per instructions above
const b2Bucket = 'bucket-name'; // configure this as per instructions above
const b2UrlPath = `/file/${b2Bucket}/`;
addEventListener('fetch', event => {
    return event.respondWith(fileReq(event));
});

// define the file extensions we wish to add basic access control headers to
0const corsFileTypes = ['png', 'jpg', 'gif', 'jpeg', 'webp'];

// backblaze returns some additional headers that are useful for debugging, but unnecessary in production. We can remove these to save some size
const removeHeaders = [
    'x-bz-content-sha1',
    'x-bz-file-id',
    'x-bz-file-name',
    'x-bz-info-src_last_modified_millis',
    'X-Bz-Upload-Timestamp',
    'Expires'
];
const expiration = 31536000; // override browser cache for images - 1 year

// define a function we can re-use to fix headers
const fixHeaders = function(url, status, headers){
    let newHdrs = new Headers(headers);
    // add basic cors headers for images
    if(corsFileTypes.includes(url.pathname.split('.').pop())){
        newHdrs.set('Access-Control-Allow-Origin', '*');
    }
    // override browser cache for files when 200
    if(status === 200){
        newHdrs.set('Cache-Control', "public, max-age=" + expiration);
    }else{
        // only cache other things for 5 minutes
        newHdrs.set('Cache-Control', 'public, max-age=300');
    }
    // set ETag for efficient caching where possible
    const ETag = newHdrs.get('x-bz-content-sha1') || newHdrs.get('x-bz-info-src_last_modified_millis') || newHdrs.get('x-bz-file-id');
    if(ETag){
        newHdrs.set('ETag', ETag);
    }
    // remove unnecessary headers
    removeHeaders.forEach(header => {
        newHdrs.delete(header);
    });
    return newHdrs;
};
async function fileReq(event){
    const cache = caches.default; // Cloudflare edge caching
    const url = new URL(event.request.url);
    if(url.host === b2Domain && !url.pathname.startsWith(b2UrlPath)){
        url.pathname = b2UrlPath + url.pathname;
    }
    let response = await cache.match(url); // try to find match for this request in the edge cache
    if(response){
        // use cache found on Cloudflare edge. Set X-Worker-Cache header for helpful debug
        let newHdrs = fixHeaders(url, response.status, response.headers);
        newHdrs.set('X-Worker-Cache', "true");
        return new Response(response.body, {
            status: response.status,
            statusText: response.statusText,
            headers: newHdrs
        });
    }
    // no cache, fetch image, apply Cloudflare lossless compression
    response = await fetch(url, {cf: {polish: "lossless"}});
    let newHdrs = fixHeaders(url, response.status, response.headers);

  if(response.status === 200){

    response = new Response(response.body, {
      status: response.status,
      statusText: response.statusText,
      headers: newHdrs
    });
  }else{
    response = new Response('File not found!', { status: 404 })
  }

    event.waitUntil(cache.put(url, response.clone()));
    return response;
}

```

注意代码的第`2-3`行:

```js
const b2Domain = 'img.domain.com'; // configure this as per instructions above
const b2Bucket = 'bucket-name'; // configure this as per instructions above
```

- b2Domain:修改成自己图床的域名。
- b2Bucket，修改成自己的`bucket`名字。

修改后点击按钮**Save and Depoly**保存。

保存后还不能直接使用，我们需要配置下规则。

进入域名下的控制面板。点击菜单**Workers**进入面板，然后点击**Add route**按钮。

添加路由为之前配置的图片服务器地址，后面使用`*`匹配该域名下的所有所有内容。 并在*Worker*处选择刚才创建的*Workers*名称。 

配置好少，稍等片刻生效。即可以通过最终的图片地址访问了:

```
https://img.maojun.xyz/foo.png
```

> 注意：配置`Workers`后上面配置的`Page ruler`规则会失效。配置了`Workers`规则会先走。


## 配置防盗链

可以配置`Referer`来判断是否是自己的来自自己域名，如果不是则都拦截。 由于`Referer`是可以修改的，所以这只能简单的进行拦截。

进入域名下的面板，点击菜单**Firewall**进入服务。点击**+ Create firewall rule**进入防火墙规则服务。 

填写规则名称和规则匹配条件：

| Field    | Operator          | Value          |
| :------- | :---------------- | :------------- |
| Referer  | does not contains | maojun.xyz     |
| URI Full | contains          | img.maojun.xyz |

> (not http.referer contains "maojun.xyz" and http.request.full_uri contains "img.maojun.xyz")

上面的规则是： 如果`URI`全路径中包含了`img.maojun.xyz`内容，**同时**`Referer`不包含`maojun.xyz`则拦截。 由于这个图床我暂时只有一个域名即`maojun.xyz`在用，如果你有多个域名使用同一图床，可以在上面的规则中填加多个域名放行。

配置完以上后，就可以通过优化后的图片地址`https://img.maojun.xyz/test.png`访问了。

(本文完）


[^1]: [使用 backblaze-B2 搭建自己的图床](https://maojun.xyz/blog/2020/02/21/使用backblaze-B2搭建自己的图床.html)

[^2]: [Cloudflare - The Web Performance & Security Company | Cloudflare](https://cloudflare.com/)

