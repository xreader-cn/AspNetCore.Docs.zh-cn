# <a name="aspnet-core-url-rewriting-sample"></a>ASP.NET Core URL 重写示例

此示例说明 ASP.NET Core URL 重写中间件的用法。 该应用演示 URL 重定向和 URL 重写选项。

运行示例时，如果向请求 URL 应用了其中一个规则，则非文件响应将返回重写或重定向的 URL。 对于 XML 和文本文件示例，静态文件中间件将在中间件重写请求 URL 之后提供该文件。

## <a name="examples-in-this-sample"></a>此示例中的示例

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - 成功状态代码：302 (已找到)
  - 示例（重定向）：/redirect-rule/{capture_group} 到 /redirected/{capture_group}  
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - 成功状态代码：200 (正常)
  - 示例（重写）：/rewrite-rule/{capture_group_1}/{capture_group_2} 到 /rewritten?var1={capture_group_1}&var2={capture_group_2}  
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - 成功状态代码：302 (已找到)
  - 示例（重定向）：/apache-mod-rules-redirect/{capture_group} 到 /redirected?id={capture_group}  
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - 成功状态代码：200 (正常)
  - 示例（重写）：/iis-rules-rewrite/{capture_group} 到 /rewritten?id={capture_group}  
* `Add(RedirectXmlFileRequests)`
  - 成功状态代码：301 (永久移动)
  - 示例（重定向）：/file.xml 到 /xmlfiles/file.xml  
* `Add(RewriteTextFileRequests)`
  - 成功状态代码：200 (正常)
  - 示例（重写）：/some_file.txt 到 /file.txt  
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - 成功状态代码：301 (永久移动)
  - 示例（重定向）：/image.png 到 /png-images/image.png  
  - 示例（重定向）：/image.jpg 到 /jpg-images/image.jpg  

## <a name="use-a-physicalfileprovider"></a>使用 PhysicalFileProvider

也可通过创建 `IFileProvider` 以传递给 `PhysicalFileProvider` 和 `AddApacheModRewrite()` 方法来获取 `AddIISUrlRewrite()`：

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a>安全的重定向扩展

此示例包含应用的 `WebHostBuilder` 配置以使用 URL（`https://localhost:5001`、`https://localhost`）和测试证书 (testCert.pfx) 来帮助探索安全重定向方法  。 如果服务器已分配或正在使用端口 443，则 `https://localhost` 示例不起作用 &mdash; 在 Program.cs 文件的 `ListenOptions` 方法中删除端口 443 的 `CreateWebHostBuilder` 或在服务器上取消绑定端口 443，以便 Kestrel 可以使用该端口  。

| 方法                           | 状态代码 |    端口    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     301     | null (465) |
| `.AddRedirectToHttps()`          |     302     | null (465) |
| `.AddRedirectToHttps(301)`       |     301     | null (465) |
| `.AddRedirectToHttps(301, 5001)` |     301     |    5001    |
