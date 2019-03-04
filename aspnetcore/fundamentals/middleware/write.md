---
title: 写入自定义 ASP.NET Core 中间件
author: rick-anderson
description: 了解如何写入自定义 ASP.NET Core 中间件。
ms.author: riande
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: 2c5577394a10370d92c8a83f9d806b63f3245c8b
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56744899"
---
# <a name="write-custom-aspnet-core-middleware"></a>写入自定义 ASP.NET Core 中间件

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)

中间件是一种装配到应用管道以处理请求和响应的软件。 ASP.NET Core 提供了一组丰富的内置中间件组件，但在某些情况下，你可能需要写入自定义中间件。

## <a name="middleware-class"></a>中间件类

通常，中间件封装在类中，并且通过扩展方法公开。 请考虑以下中间件，该中间件通过查询字符串设置当前请求的区域性：

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

以上示例代码用于演示创建中间件组件。 有关 ASP.NET Core 的内置本地化支持，请参阅 <xref:fundamentals/localization>。

可通过传入区域性测试中间件。 例如 `http://localhost:7997/?culture=no`。

以下代码将中间件委托移动到类：

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

中间件 `Task` 方法的名称必须是 `Invoke`。 在 ASP.NET Core 2.0 或更高版本中，该名称可以为 `Invoke` 或 `InvokeAsync`。

::: moniker-end

## <a name="middleware-extension-method"></a>中间件扩展方法

以下扩展方法通过 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 公开中间件：

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

以下代码通过 `Startup.Configure` 调用中间件：

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

中间件应通过在其构造函数中公开其依赖项来遵循[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。 在每个应用程序生存期构造一次中间件。 如果需要与请求中的中间件共享服务，请参阅[按请求依赖项](#per-request-dependencies)部分。

中间件组件可通过构造函数参数从[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 解析其依赖项。 [UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) 也可直接接受其他参数。

## <a name="per-request-dependencies"></a>按请求依赖项

由于中间件是在应用启动时构造的，而不是按请求构造的，因此在每个请求过程中，中间件构造函数使用的范围内生存期服务不与其他依赖关系注入类型共享。 如果必须在中间件和其他类型之间共享范围内服务，请将这些服务添加到 `Invoke` 方法的签名。 `Invoke` 方法可接受由 DI 填充的其他参数：

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
