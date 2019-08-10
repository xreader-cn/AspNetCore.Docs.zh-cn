# <a name="aspnet-core-response-caching-sample"></a>ASP.NET Core 响应缓存示例

本示例演示使用 ASP.NET Core [响应缓存中间件](https://docs.microsoft.com/aspnet/core/performance/caching/middleware)。

应用使用其索引页进行响应, 包括配置`Cache-Control`缓存行为的标头。 应用程序还会将`Vary`标头设置为将缓存配置为仅`Accept-Encoding`当后续请求的标头与原始请求中的标头匹配时才为响应提供服务。

运行示例时, 将在存储和缓存索引页时从缓存中为长达10秒提供服务。

测试缓存行为:

* 不要使用浏览器测试缓存行为。 浏览器通常会在重新加载时添加一个缓存控制标头, 以阻止中间件提供缓存页面。 例如, `Cache-Control`浏览器可能会添加值为`max-age=0`的标头。
* 使用允许显式设置请求标头的开发人员工具, 如<a href="https://www.telerik.com/fiddler">Fiddler</a>或<a href="https://www.getpostman.com/">Postman</a>。
