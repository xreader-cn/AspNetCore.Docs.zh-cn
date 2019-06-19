---
title: 将 HTTP 处理程序和模块迁移到 ASP.NET Core 中间件
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: 84381210910c66a7d121120b8c6b0f046cae8c4f
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207807"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>将 HTTP 处理程序和模块迁移到 ASP.NET Core中间件

这篇文章演示如何迁移现有的 ASP.NET [HTTP 模块和处理程序 system.webserver](/iis/configuration/system.webserver/)到 ASP.NET Core [中间件](xref:fundamentals/middleware/index)。

## <a name="modules-and-handlers-revisited"></a>模块和处理程序再次讨论

在继续之前到 ASP.NET Core 中间件，让我们首先会扼要重述 HTTP 模块和处理程序的工作原理：

![模块处理程序](http-modules/_static/moduleshandlers.png)

**处理程序是：**

* 类实现[IHttpHandler](/dotnet/api/system.web.ihttphandler)

* 用于处理请求的给定的文件名或扩展名，如 *。 报表*

* [配置](/iis/configuration/system.webserver/handlers/)在*Web.config*

**模块包括：**

* 类实现[IHttpModule](/dotnet/api/system.web.ihttpmodule)

* 为每个请求调用

* 能够短路 （停止进一步处理的请求）

* 无法添加到 HTTP 响应，或创建其自己

* [配置](/iis/configuration/system.webserver/modules/)在*Web.config*

**在其中模块处理传入的请求的顺序取决于：**

1. [应用程序生命周期](https://msdn.microsoft.com/library/ms227673.aspx)，这是由 ASP.NET 激发的系列事件：[BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest)， [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)，等等。每个模块可以创建一个或多个事件的处理程序。

2. 对于同一个事件，在其中配置中的顺序*Web.config*。

除了模块，可以添加到生命周期事件的处理程序您*Global.asax.cs*文件。 已配置的模块中的处理程序后运行这些处理程序。

## <a name="from-handlers-and-modules-to-middleware"></a>从处理程序和模块到中间件

**中间件是 HTTP 模块和处理程序比简单得多：**

* 模块、 处理程序、 *Global.asax.cs*， *Web.config* （除 IIS 配置） 和应用程序生命周期都消失了

* 模块和处理程序的角色具有接管中间件

* 中间件配置为使用代码而不在*Web.config*

* [管道分支](xref:fundamentals/middleware/index#use-run-and-map)允许将请求发送到特定的中间件，根据不仅 URL 还在请求标头、 查询字符串，等等。

**中间件是非常类似于模块：**

* 在为每个请求的主体中调用

* 能够通过短路请求[不将请求传递给下一个中间件](#http-modules-shortcircuiting-middleware)

* 能够创建他们自己的 HTTP 响应

**中间件和模块按不同顺序处理：**

* 中间件的顺序基于在其中插入到请求管道，而模块的顺序主要基于的顺序[应用程序生命周期](https://msdn.microsoft.com/library/ms227673.aspx)事件

* 中间件的响应的顺序是反向从，对于请求，而模块的顺序是相同的请求和响应

* 请参阅[使用 IApplicationBuilder 创建中间件管道](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![中间件](http-modules/_static/middleware.png)

请注意如何在上图中中的身份验证中间件设置短路请求。

## <a name="migrating-module-code-to-middleware"></a>迁移到中间件模块代码

现有的 HTTP 模块将类似于此：

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

中所示[中间件](xref:fundamentals/middleware/index)页上，ASP.NET Core 中间件是公开的类`Invoke`方法拍摄`HttpContext`并返回`Task`。 在新的中间件将如下所示：

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

上一部分中执行前面的中间件模板[编写中间件](xref:fundamentals/middleware/write)。

*MyMiddlewareExtensions*帮助器类，可以更轻松地配置中间件中的你`Startup`类。 `UseMyMiddleware`方法将中间件类添加到请求管道。 在中间件的构造函数中注入获取所需的中间件服务。

<a name="http-modules-shortcircuiting-middleware"></a>

你的模块可能会终止，例如，如果用户未经授权的请求：

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

中间件处理这是通过不调用`Invoke`管道中的下一个中间件上。 请注意这不会完全终止请求，因为响应其返回到管道的方式时，仍要调用前面中间件。

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

当你迁移到新中间件模块的功能时，你可能会发现你的代码不会编译，因为`HttpContext`类中 ASP.NET Core 已显著更改。 [更高版本上](#migrating-to-the-new-httpcontext)，你将了解如何将迁移到新的 ASP.NET Core HttpContext。

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>迁移模块插入到请求管道

HTTP 模块通常会添加到请求管道使用*Web.config*:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

将对此进行转换[添加新中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)到请求管道中在`Startup`类：

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

在其中插入新中间件在管道中的确切位置取决于它作为模块处理的事件 (`BeginRequest`，`EndRequest`等) 及其在列表中的模块中的顺序*Web.config*。

如前面所述，没有任何应用程序生命周期中 ASP.NET Core，中间件处理响应的顺序不同于使用模块的顺序。 这可能使你订购的决策更具挑战性。

如果排序将为问题，无法将你的模块分为多个中间件组件可单独进行排序。

## <a name="migrating-handler-code-to-middleware"></a>迁移到中间件的处理程序代码

HTTP 处理程序如下所示：

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

在 ASP.NET Core 项目中，你将翻译以下到中间件类似于以下内容：

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

此中间件是非常类似于对应于模块的中间件。 唯一的差别是，此处没有不需要调用`_next.Invoke(context)`。 这样很合理，因为该处理不程序末尾的请求管道，因此将对任何要调用的下一个中间件。

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>迁移的处理程序插入到请求管道

配置 HTTP 处理程序中完成*Web.config*和外观如下所示：

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

无法将此转换通过将新的处理程序中间件添加到请求管道中在`Startup`类，类似于从模块转换为中间件。 该方法的问题是，它会将所有请求都发送到新的处理程序中间件。 但是，你只想与给定扩展插件的请求来访问中间件。 可让你已经有了 HTTP 处理程序使用相同的功能。

一种解决方案是分支的扩展名为给定的请求管道使用`MapWhen`扩展方法。 执行此操作在同一个`Configure`方法，添加在其他中间件：

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` 使用这些参数：

1. 采用的 lambda `HttpContext` ，并返回`true`如果请求应停机分支。 这意味着可以分支请求而不仅仅是基于其的扩展名，而且还在请求标头、 查询字符串参数，等等。

2. 采用的 lambda `IApplicationBuilder` ，并将添加的分支的所有中间件。 这意味着您可以添加其他中间件到分支处理程序中间件的前面。

将所有请求; 调用分支之前，中间件添加到管道分支会对其产生任何影响。

## <a name="loading-middleware-options-using-the-options-pattern"></a>正在加载使用选项模式的中间件选项

一些模块和处理程序具有中存储的配置选项*Web.config*。但是，在 ASP.NET Core 中新的配置模型使用代替了*Web.config*。

新[配置系统](xref:fundamentals/configuration/index)为你提供这些选项，若要解决此问题：

* 直接注入到中间件的选项，如中所示[下一节](#loading-middleware-options-through-direct-injection)。

* 使用[选项模式](xref:fundamentals/configuration/options):

1. 创建一个类来保存中间件的选项，例如：

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. 存储选项的值

   配置系统，可存储选项任意位置所需的值。 但是，多数场所使用*appsettings.json*，因此我们将这种方法：

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *MyMiddlewareOptionsSection*下面是部分名称。 它不一定要与选项类的名称相同。

3. 与选项类关联的选项值

    选项模式使用 ASP.NET Core 依赖关系注入框架将选项类型相关联 (如`MyMiddlewareOptions`) 与`MyMiddlewareOptions`具有实际选项对象。

    更新你`Startup`类：

   1. 如果您使用的*appsettings.json*，将其添加到中的配置生成器`Startup`构造函数：

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. 配置选项服务：

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. 将你的选项与选项类相关联：

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. 注入到中间件构造函数的选项。 这是类似于将注入到控制器的选项。

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   [UseMiddleware](#http-modules-usemiddleware)添加到中间件的扩展方法`IApplicationBuilder`负责处理的依赖关系注入。

   这并不局限于`IOptions`对象。 这种方式，可以注入中间件需要的任何其他对象。

## <a name="loading-middleware-options-through-direct-injection"></a>加载通过直接注入的中间件选项

选项模式具有的优势在于它会创建松散耦合选项值和其使用者之间。 一旦与实际选项值关联的选项类，其他任何类可获得访问通过依赖关系注入框架的选项。 没有无需传递选项值。

这将分解但如果想要使用不同选项两次，使用同一个中间件。 例如授权中间件使用允许不同的角色的不同分支中。 不能将两个不同的选项对象与一个选项类相关联。

解决方法是获取使用中的实际选项值的选项对象在`Startup`类，并将这些直接向中间件的每个实例。

1. 添加到第二个密钥*appsettings.json*

   若要添加另一组选项*appsettings.json*文件，请使用新密钥来唯一识别它：

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. 检索选项值，并将其传递到中间件。 `Use...` （其中向管道添加中间件） 扩展方法是将选项值传递的合理位置： 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. 启用中间件需要包含 options 参数。 提供的一个重载`Use...`扩展方法 (它接受的 options 参数并将其传递给`UseMiddleware`)。 当`UseMiddleware`调用带参数，它将参数传递给中间件构造函数实例化中间件对象时。

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   请注意这将中的选项对象的包装`OptionsWrapper`对象。 这会实现`IOptions`，如所需的中间件构造函数。

## <a name="migrating-to-the-new-httpcontext"></a>迁移到新的 HttpContext

你此前看到的`Invoke`中间件中的方法采用一个参数类型的`HttpContext`:

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` 已显著更改 ASP.NET Core 中。 本部分演示如何将最常使用的属性转换[System.Web.HttpContext](/dotnet/api/system.web.httpcontext)对新`Microsoft.AspNetCore.Http.HttpContext`。

### <a name="httpcontext"></a>HttpContext

**Httpcontext.items 进行更换**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**唯一请求 ID （没有 System.Web.HttpContext 对应）**

为你的唯一 id 用于每个请求。 包括在日志中非常有用。

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext.Request

**HttpContext.Request.HttpMethod**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext.Request.QueryString**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext.Request.Url**并**HttpContext.Request.RawUrl**将转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext.Request.IsSecureConnection**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext.Request.UserHostAddress**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext.Request.Cookies**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext.Request.RequestContext.RouteData**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext.Request.Headers**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext.Request.UserAgent**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext.Request.UrlReferrer**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext.Request.ContentType**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext.Request.Form**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> 仅当内容的子类型为读取窗体值*x-www 的窗体的 urlencoded*或*窗体数据*。

**HttpContext.Request.InputStream**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> 仅在处理程序类型中间件在管道末尾使用此代码。
>
>每个请求仅一次如上所示，可以读取原始正文。 尝试在第一次读取后读取正文的中间件将读取正文为空。
>
>这不适用于读取窗体，如前文所述，因为这缓冲区中完成。

### <a name="httpcontextresponse"></a>HttpContext.Response

**HttpContext.Response.Status**并**HttpContext.Response.StatusDescription**将转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext.Response.ContentEncoding**并**HttpContext.Response.ContentType**将转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext.Response.ContentType**上其自身还会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext.Response.Output**会转换为：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext.Response.TransmitFile**

提供一个文件进行了讨论[此处](../fundamentals/request-features.md#middleware-and-request-features)。

**HttpContext.Response.Headers**

发送响应标头十分复杂，这一事实，如果您将它们设置到响应正文写入任何内容后，它们将不会发送。

解决方案是设置将写入到响应开始之前先调用右侧的回调方法。 最好的做法是在开头`Invoke`中间件中的方法。 它是此回调方法，设置响应标头。

下面的代码设置调用的回调方法`SetHeaders`:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetHeaders`回调方法将如下所示：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext.Response.Cookies**

Cookie 的浏览器中为旅行*Set-cookie*响应标头。 因此，发送 cookie 用于发送响应标头需要使用相同的回调：

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetCookies`回调方法将如下所示：

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>其他资源

* [HTTP 处理程序和 HTTP 模块概述](/iis/configuration/system.webserver/)
* [配置](xref:fundamentals/configuration/index)
* [应用程序启动](xref:fundamentals/startup)
* [中间件](xref:fundamentals/middleware/index)
