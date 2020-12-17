---
title: 在 ASP.NET Core 中访问 HttpContext
author: coderandhiker
description: 了解如何在 ASP.NET Core 中访问 HttpContext。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
no-loc:
- appsettings.json
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
uid: fundamentals/httpcontext
ms.openlocfilehash: d5ada72bb03cd74cf77c31450e5eafb9c2841049
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506989"
---
# <a name="access-httpcontext-in-aspnet-core"></a>在 ASP.NET Core 中访问 HttpContext

ASP.NET Core 应用通过 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 接口及其默认实现 <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> 访问 `HttpContext`。 只有在需要访问服务内的 `HttpContext` 时，才有必要使用 `IHttpContextAccessor`。

## <a name="use-httpcontext-from-no-locrazor-pages"></a>通过 Razor Pages 使用 HttpContext

Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 公开 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> 属性：

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-no-locrazor-view"></a>通过 Razor 视图使用 HttpContext

Razor 视图通过视图上的 [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) 属性直接公开 `HttpContext`。 下面的示例使用 Windows 身份验证检索 Intranet 应用中的当前用户名：

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>通过控制器使用 HttpContext

控制器公开 [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) 属性：

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a>通过中间件使用 HttpContext

使用自定义中间件组件时，`HttpContext` 传递到 `Invoke` 或 `InvokeAsync` 方法，在中间件配置后可供访问：

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>通过自定义组件使用 HttpContext

对于需要访问 `HttpContext` 的其他框架和自定义组件，建议使用内置的[依赖项注入](xref:fundamentals/dependency-injection)容器来注册依赖项。 依赖项注入容器向任意类提供 `IHttpContextAccessor`，以供类在自己的构造函数中将它声明为依赖项：

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

如下示例中：

* `UserRepository` 声明自己对 `IHttpContextAccessor` 的依赖。
* 当依赖项注入容器解析依赖链并创建 `UserRepository` 实例时，就会注入依赖项。

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a>从后台线程访问 HttpContext

`HttpContext` 不是线程安全型。 在处理请求之外读取或写入 `HttpContext` 的属性可能会导致 <xref:System.NullReferenceException>。

> [!NOTE]
> 如果应用生成偶发的 `NullReferenceException` 错误，请评审启动后台处理的部分代码，或者在请求完成后继续处理的部分代码。 查找诸如将控制器方法定义为 `async void` 的错误。

要使用 `HttpContext` 数据安全地执行后台工作，请执行以下操作：

* 在请求处理过程中复制所需的数据。
* 将复制的数据传递给后台任务。

要避免不安全代码，请勿将 `HttpContext` 传递给执行后台工作的方法。 而是传递所需要的数据。 在以下示例中，调用 `SendEmailCore`，开始发送电子邮件。 将 `correlationId` 传递到 `SendEmailCore`，而不是 `HttpContext`。 代码执行不会等待 `SendEmailCore` 完成：

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
```

## <a name="no-locblazor-and-shared-state"></a>Blazor 和共享状态

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]
