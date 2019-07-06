# <a name="aspnet-core-response-caching-sample"></a>ASP.NET Core 响应缓存示例

本示例演示使用 ASP.NET Core [响应缓存中间件](https://docs.microsoft.com/aspnet/core/performance/caching/middleware)。

应用的响应与它的索引页，其中包括`Cache-Control`标头，若要配置的缓存行为。 应用程序还会设置`Vary`标头来配置缓存，以提供响应才`Accept-Encoding`后续请求标头中的匹配的原始请求。

运行示例时，是从缓存存储和缓存最多 10 秒时提供的索引页。

若要测试的缓存行为：

* 不要使用浏览器测试的缓存行为。 浏览器通常添加在阻止中间件提供缓存的页面的重载的缓存控制标头。 例如，`Cache-Control`标头值为`max-age=0`) 可能会添加由浏览器。
* 使用一个开发人员工具来显式设置的请求标头，如<a href="https://www.telerik.com/fiddler">Fiddler</a>或<a href="https://www.getpostman.com/">Postman</a>。
