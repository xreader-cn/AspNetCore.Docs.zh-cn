# <a name="response-compression-sample-application-aspnet-core-2x"></a>响应压缩示例应用程序 (ASP.NET Core 2.x)

此示例演示如何使用 ASP.NET Core 2.x 响应压缩中间件来压缩 HTTP 响应。 此示例演示 Gzip、 Brotli 和自定义压缩的文本和图像响应的提供程序，并演示如何添加压缩的 MIME 类型。 有关 ASP.NET Core 1.x 示例，请参阅[响应压缩示例应用程序 (ASP.NET Core 1.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples/1.x)。

## <a name="examples-in-this-sample"></a>此示例中的示例

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -乱数假文文本文件响应为 2,044 个字节，压缩为 ~ 979 字节。
    * **/testfile1kb.txt** -将压缩到 ~ 36 个字节的文本文件响应 1,033 字节。
    * **/ 渗透**-每次间隔 1 秒作为单个字符发出的响应。
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -乱数假文文本文件响应为 2,044 个字节，压缩为 ~ 927 字节。
    * **/testfile1kb.txt** -将压缩到 ~ 47 个字节的文本文件响应 1,033 字节。
    * **/ 渗透**-每次间隔 1 秒作为单个字符发出的响应。
  * `image/svg+xml`
    * **/banner.svg** -将压缩为 ~ 4,459 字节的可缩放向量图形 (SVG) 图像响应 9,707 字节。
* `CustomCompressionProvider`<br>演示如何实现与中间件使用的自定义压缩提供程序。

当请求包括`Accept-Encoding`标头和响应压缩操作成功，则中间件会自动添加`Vary: Accept-Encoding`到响应的标头。 `Vary`标头会指示缓存维护的响应的替代值为基础的多个副本`Accept-Encoding`，因此这两种压缩 （Gzip 或 Brotli） 和未压缩的版本存储在缓存中的系统可以接受压缩或未压缩的响应。

## <a name="use-the-sample"></a>使用示例

1. 使请求使用[Fiddler](http://www.telerik.com/fiddler)， [Firebug](http://getfirebug.com/)，或[Postman](https://www.getpostman.com/)到应用程序而不`Accept-Encoding`标头并记下响应有效负载，响应大小和响应标头。
1. 添加`Accept-Encoding: br`或`Accept-Encoding: gzip`标头并记下压缩的响应大小和响应标头。 响应大小降至，和`Content-Encoding`响应标头包含由中间件，该值指示该使用任一 Gzip 压缩或 Brotli 发生。 要查看在响应正文的乱数假文或**testfile1kb.txt**响应，你将看到文本是压缩且不可读。
1. 添加`Accept-Encoding: mycustomcompression`标头并记下响应标头。 `CustomCompressionProvider`是一个空实现，实际上不会压缩响应，但您可以创建自定义压缩流包装器`CreateStream()`方法。
