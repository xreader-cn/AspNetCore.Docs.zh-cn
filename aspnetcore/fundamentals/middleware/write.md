---
title: 写入自定义 ASP.NET Core 中间件
author: rick-anderson
description: 了解如何写入自定义 ASP.NET Core 中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/18/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/write
ms.openlocfilehash: 52985917c34ebf007c0d205625956c772456ee2b
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635250"
---
# <a name="write-custom-aspnet-core-middleware"></a>写入自定义 ASP.NET Core 中间件

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)

中间件是一种装配到应用管道以处理请求和响应的软件。 ASP.NET Core 提供了一组丰富的内置中间件组件，但在某些情况下，你可能需要写入自定义中间件。

> [!NOTE]
> 本主题介绍如何编写基于约定的中间件。 有关使用强类型和按请求激活的方法，请参阅 <xref:fundamentals/middleware/extensibility>。

## <a name="middleware-class"></a>中间件类

通常，中间件封装在类中，并且通过扩展方法公开。 请考虑以下中间件，该中间件通过查询字符串设置当前请求的区域性：

[!code-csharp[](write/snapshot/StartupCulture.cs)]

以上示例代码用于演示创建中间件组件。 有关 ASP.NET Core 的内置本地化支持，请参阅 <xref:fundamentals/localization>。

通过传入区域性测试中间件。 例如，请求 `https://localhost:5001/?culture=no`。

以下代码将中间件委托移动到类：

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

必须包括中间件类：

* 具有类型为 <xref:Microsoft.AspNetCore.Http.RequestDelegate> 的参数的公共构造函数。
* 名为 `Invoke` 或 `InvokeAsync` 的公共方法。 此方法必须：
  * 返回 `Task`。
  * 接受类型 <xref:Microsoft.AspNetCore.Http.HttpContext> 的第一个参数。
  
构造函数和 `Invoke`/`InvokeAsync` 的其他参数由[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 填充。

## <a name="middleware-dependencies"></a>中间件依赖项

中间件应通过在其构造函数中公开其依赖项来遵循[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。 在每个应用程序生存期构造一次中间件。 如果需要与请求中的中间件共享服务，请参阅[按请求中间件依赖项](#per-request-middleware-dependencies)部分。

中间件组件可通过构造函数参数从[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 解析其依赖项。 [UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) 也可直接接受其他参数。

## <a name="per-request-middleware-dependencies"></a>按请求中间件依赖项

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

[生存期和注册选项](xref:fundamentals/dependency-injection#lifetime-and-registration-options)包含范围内生存期服务的中间件的完整示例。

## <a name="middleware-extension-method"></a>中间件扩展方法

以下扩展方法通过 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 公开中间件：

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

以下代码通过 `Startup.Configure` 调用中间件：

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>其他资源

* [生存期和注册选项](xref:fundamentals/dependency-injection#lifetime-and-registration-options)包含范围内、临时性和单一实例生存期服务的中间件的完整示例  。
* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
