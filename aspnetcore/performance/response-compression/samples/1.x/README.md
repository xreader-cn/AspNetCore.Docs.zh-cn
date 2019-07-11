# <a name="response-compression-sample-application-aspnet-core-1x"></a>响应压缩示例应用程序 (ASP.NET Core 1.x)

此示例演示如何使用 ASP.NET Core 1.x 响应的压缩中间件来压缩 HTTP 响应中的。 此示例演示 Gzip 和自定义压缩的文本和图像响应的提供程序，并演示如何添加压缩的 MIME 类型。 有关 ASP.NET Core 2.x 示例，请参阅[响应压缩示例应用程序 (ASP.NET Core 2.x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x)。

## <a name="examples-in-this-sample"></a>此示例中的示例

* `GzipCompressionProvider`
  * `text/plain`
    * **/** -将压缩为 927 字节的 2,044 字节乱数假文文本文件响应
    * **/testfile1kb.txt** -1,033 将压缩为 47 个字节的字节的文本文件响应
    * **/ 渗透**-每次间隔 1 秒作为单个字符发出的响应
  * `image/svg+xml`
    * **/banner.svg** -9,707 将压缩为 4,459 字节的字节的可缩放向量图形 (SVG) 图像响应
* `CustomCompressionProvider`<br>演示如何实现与中间件使用的自定义压缩提供程序

当请求包括`Accept-Encoding`标头，该示例添加`Vary: Accept-Encoding`到响应的标头。 `Vary`标头会指示缓存维护的响应的替代值为基础的多个副本`Accept-Encoding`，因此需压缩 (gzip) 和未压缩的版本存储在缓存中的系统可以接受压缩或未压缩的响应。

## <a name="using-the-sample"></a>使用示例

1. 使请求使用[Fiddler](https://www.telerik.com/fiddler)， [Firebug](https://getfirebug.com/)，或[Postman](https://www.getpostman.com/)到应用程序而不`Accept-Encoding`标头并记下响应有效负载，响应大小和响应标头。
1. 添加`Accept-Encoding: gzip`标头并记下压缩的响应大小和响应标头。 请参阅删除，响应大小和`Content-Encoding: gzip`响应标头包含示例应用。 要查看在响应正文的乱数假文或**testfile1kb.txt**响应，你将看到文本是压缩且不可读。
1. 添加`Accept-Encoding: mycustomcompression`标头并记下响应标头。 `CustomCompressionProvider`是一个空实现，实际上不会压缩响应，但您可以创建自定义压缩流包装器`CreateStream()`方法。
