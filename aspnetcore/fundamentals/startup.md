---
title: ASP.NET Core 中的应用启动
author: rick-anderson
description: 了解 ASP.NET Core 中的 Startup 类如何配置服务和应用的请求管道。
ms.author: riande
ms.custom: mvc
ms.date: 8/7/2019
uid: fundamentals/startup
ms.openlocfilehash: 0ea3965f73f4b0334810bc9ec2910b0c9364a7ba
ms.sourcegitcommit: d8b12cc1716ee329d7bd2300e201b61e15d506ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71942868"
---
# <a name="app-startup-in-aspnet-core"></a>ASP.NET Core 中的应用启动

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra)、[Luke Latham](https://github.com/guardrex) 和 [Steve Smith](https://ardalis.com)

`Startup` 类配置服务和应用的请求管道。

## <a name="the-startup-class"></a>Startup 类

ASP.NET Core 应用使用 `Startup` 类，按照约定命名为 `Startup`。 `Startup` 类：

* 可选择性地包括 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 方法以配置应用的服务  。 服务是一个提供应用功能的可重用组件。 在 `ConfigureServices` 中配置配置（也称为“注册”）并通过[依存关系注入 (DI)](xref:fundamentals/dependency-injection) 或 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> 在整个应用中使用  。
* 包括 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 方法以创建应用的请求处理管道。

在应用启动时，ASP.NET Core 运行时会调用 `ConfigureServices` 和 `Configure`：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

前面的示例适用于 [Razor Pages](xref:razor-pages/index)；MVC 版本类似。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

::: moniker-end

在构建应用[主机](xref:fundamentals/index#host)时指定 `Startup` 类。 通常通过在主机生成器上调用 [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) 方法来指定 `Startup` 类：

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

主机提供 `Startup` 类构造函数可用的某些服务。 应用通过 `ConfigureServices` 添加其他服务。 主机和应用服务都可以在 `Configure` 和整个应用中使用。

使用 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 时，只能将以下服务类型注入 `Startup` 构造函数：

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

在调用 `Configure` 方法之前，大多数服务都不可用。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

主机提供 `Startup` 类构造函数可用的某些服务。 应用通过 `ConfigureServices` 添加其他服务。 然后，主机和应用服务都可以在 `Configure` 和整个应用中使用。

在 `Startup` 类中[注入依赖关系](xref:fundamentals/dependency-injection)的常见用途为注入：

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> 按环境配置服务。
* <xref:Microsoft.Extensions.Configuration.IConfiguration> 读取配置。
* <xref:Microsoft.Extensions.Logging.ILoggerFactory> 在记录器中创建 `Startup.ConfigureServices`。

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

在调用 `Configure` 方法之前，大多数服务都不可用。

::: moniker-end

### <a name="multiple-startup"></a>多启动

应用为不同的环境（例如，`StartupDevelopment`）单独定义 `Startup` 类时，相应的 `Startup` 类会在运行时被选中。 优先考虑名称后缀与当前环境相匹配的类。 如果应用在开发环境中运行并包含 `Startup` 类和 `StartupDevelopment` 类，则使用 `StartupDevelopment` 类。 有关详细信息，请参阅[使用多个环境](xref:fundamentals/environments#environment-based-startup-class-and-methods)。

请参阅[主机](xref:fundamentals/index#host)，了解有关主机的详细信息。 有关在启动过程中处理错误的信息，请参阅[启动异常处理](xref:fundamentals/error-handling#startup-exception-handling)。

## <a name="the-configureservices-method"></a>ConfigureServices 方法

<xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 方法：

* 可选。
* 在 `Configure` 方法配置应用服务之前，由主机调用。
* 其中按常规设置[配置选项](xref:fundamentals/configuration/index)。

主机可能会在调用 `Startup` 方法之前配置某些服务。 有关详细信息，请参阅[主机](xref:fundamentals/index#host)。

对于需要大量设置的功能，<xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 上有 `Add{Service}` 扩展方法。 例如，**Add**DbContext、**Add**DefaultIdentity、**Add**EntityFrameworkStores 和 **Add**RazorPages：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

::: moniker-end

将服务添加到服务容器，使其在应用和 `Configure` 方法中可用。 服务通过[依赖关系注入](xref:fundamentals/dependency-injection)或 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> 进行解析。

::: moniker range="< aspnetcore-3.0"

若要详细了解 `SetCompatibilityVersion`，请参阅 [SetCompatibilityVersion](xref:mvc/compatibility-version)。

::: moniker-end

## <a name="the-configure-method"></a>Configure 方法

<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> 方法用于指定应用响应 HTTP 请求的方式。 可通过将[中间件](xref:fundamentals/middleware/index)组件添加到 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 实例来配置请求管道。 `Configure` 方法可使用 `IApplicationBuilder`，但未在服务容器中注册。 托管创建 `IApplicationBuilder` 并将其直接传递到 `Configure`。

[ASP.NET Core 模板](/dotnet/core/tools/dotnet-new)配置的管道支持：

* [开发人员异常页](xref:fundamentals/error-handling#developer-exception-page)
* [异常处理程序](xref:fundamentals/error-handling#exception-handler-page)
* [HTTP 严格传输安全性 (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [HTTPS 重定向](xref:security/enforcing-ssl)
* [静态文件](xref:fundamentals/static-files)
* ASP.NET Core [MVC](xref:mvc/overview) 和 [Razor Pages](xref:razor-pages/index)

::: moniker range="< aspnetcore-3.0"

* [一般数据保护条例 (GDPR)](xref:security/gdpr)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

前面的示例适用于 [Razor Pages](xref:razor-pages/index)；MVC 版本类似。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

::: moniker-end

每个 `Use` 扩展方法将一个或多个中间件组件添加到请求管道。 例如，<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> 配置[中间件](xref:fundamentals/middleware/index)提供[静态文件](xref:fundamentals/static-files)。

请求管道中的每个中间件组件负责调用管道中的下一个组件，或在适当情况下使链发生短路。

::: moniker range=">= aspnetcore-3.0"

可以在 `Configure` 方法签名中指定其他服务，如 `IWebHostEnvironment`、`ILoggerFactory` 或 `ConfigureServices` 中定义的任何内容。 如果这些服务可用，则会被注入。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

可以在 `Configure` 方法签名中指定其他服务，如 `IHostingEnvironment` 和 `ILoggerFactory`，或 `ConfigureServices` 中定义的任何内容。 如果这些服务可用，则会被注入。

::: moniker-end

有关如何使用 `IApplicationBuilder` 和中间件处理顺序的详细信息，请参阅 <xref:fundamentals/middleware/index>。

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>在不使用 Startup 的情况下配置服务

若要配置服务和请求处理管道，而不使用 `Startup` 类，请在主机生成器上调用 `ConfigureServices` 和 `Configure` 便捷方法。 多次调用 `ConfigureServices` 将追加到另一个。 如果存在多个 `Configure` 方法调用，则使用最后一个 `Configure` 调用。

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

::: moniker-end

## <a name="extend-startup-with-startup-filters"></a>使用 Startup 筛选器扩展 Startup

使用 <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>：

* 在应用的 [Configure](#the-configure-method) 中间件管道的开头或末尾配置中间件，而无需显式调用 `Use{Middleware}`。 `IStartupFilter` 由 ASP.NET Core 用于将默认值添加到管道的开头，而无需使应用作者显式注册默认中间件。 `IStartupFilter` 允许代表应用作者使用不同的组件调用 `Use{Middleware}`。
* 创建 `Configure` 方法的管道。 [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) 可以将中间件设置为在库添加的中间件之前或之后运行。

`IStartupFilter` 实现 <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>，即接收并返回 `Action<IApplicationBuilder>`。 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 定义用于配置应用请求管道的类。 有关详细信息，请参阅[使用 IApplicationBuilder 创建中间件管道](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)。

每个 `IStartupFilter` 可以在请求管道中添加一个或多个中间件。 筛选器按照添加到服务容器的顺序调用。 筛选器可在将控件传递给下一个筛选器之前或之后添加中间件，从而附加到应用管道的开头或末尾。

下面的示例演示如何使用 `IStartupFilter` 注册中间件。 `RequestSetOptionsMiddleware` 中间件从查询字符串参数中设置选项值：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

在 `RequestSetOptionsStartupFilter` 类中配置 `RequestSetOptionsMiddleware`：

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

在 `RequestSetOptionsStartupFilter` 类中配置 `RequestSetOptionsMiddleware`：

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

在 <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> 中的服务容器中注册 `IStartupFilter`。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

::: moniker-end

当提供 `option` 的查询字符串参数时，中间件在 ASP.NET Core 中间件呈现响应之前处理分配值。

中间件执行顺序由 `IStartupFilter` 注册顺序设置：

* 多个 `IStartupFilter` 实现可能与相同的对象进行交互。 如果顺序很重要，请将它们的 `IStartupFilter` 服务注册进行排序，以匹配其中间件应有的运行顺序。
* 库可能添加包含一个或多个 `IStartupFilter` 实现的中间件，这些实现在向 `IStartupFilter` 注册的其他应用中间件之前或之后运行。 若要在库的 `IStartupFilter` 添加的中间件之前调用 `IStartupFilter` 中间件，请执行以下操作：

  * 在库添加到服务容器之前定位服务注册。
  * 要在此后调用，请在添加库之后定位服务注册。

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>在启动时从外部程序集添加配置

通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。 有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。

## <a name="additional-resources"></a>其他资源

* [主机](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
